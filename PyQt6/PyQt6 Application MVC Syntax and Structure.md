# PyQt6 Application MVC Structure

This note shows a simple **MVC (Model-View-Controller)** architecture for a Python PyQt6 application.

MVC separates the application into:

```text
Model        -> Data and business logic
View         -> User interface
Controller   -> Connects Model and View
```

---

# 1. Recommended Project Structure

```text
pyqt6-mvc-app/
│
├── main.py
│
├── models/
│   ├── __init__.py
│   └── user_model.py
│
├── views/
│   ├── __init__.py
│   └── main_view.py
│
└── controllers/
    ├── __init__.py
    └── main_controller.py
```

The application flow:

```text
User
  │
  ▼
View
  │
  ▼
Controller
  │
  ├──────────────► Model
  │                  │
  └──────────────────┘
          Data
```

---

# 2. Model

The **Model** handles data and business logic.

File:

```text
models/user_model.py
```

```python
class UserModel:

    def __init__(self):
        self.name = ""

    def set_name(self, name):
        self.name = name

    def get_name(self):
        return self.name
```

The model should not directly depend on the UI.

Example:

```python
model = UserModel()

model.set_name("Asyraf")

print(model.get_name())
```

---

# 3. View

The **View** is responsible for displaying the UI.

File:

```text
views/main_view.py
```

```python
from PyQt6.QtWidgets import (
    QWidget,
    QVBoxLayout,
    QLabel,
    QPushButton,
    QLineEdit
)


class MainView(QWidget):

    def __init__(self):
        super().__init__()

        self.setWindowTitle("PyQt6 MVC Example")

        layout = QVBoxLayout()

        self.name_input = QLineEdit()

        self.save_button = QPushButton("Save")

        self.result_label = QLabel("Name: ")

        layout.addWidget(self.name_input)
        layout.addWidget(self.save_button)
        layout.addWidget(self.result_label)

        self.setLayout(layout)
```

The View contains UI components such as:

```python
QLabel
QPushButton
QLineEdit
QComboBox
QTableWidget
QCheckBox
```

The View should ideally not contain application business logic.

---

# 4. Controller

The **Controller** connects the View and Model.

File:

```text
controllers/main_controller.py
```

```python
class MainController:

    def __init__(self, model, view):

        self.model = model
        self.view = view

        self.connect_signals()

    def connect_signals(self):

        self.view.save_button.clicked.connect(
            self.save_name
        )

    def save_name(self):

        name = self.view.name_input.text()

        self.model.set_name(name)

        self.view.result_label.setText(
            f"Name: {self.model.get_name()}"
        )
```

The Controller receives events from the View:

```python
button.clicked.connect(function)
```

Then it can:

1. Read data from the View.
2. Process the data.
3. Update the Model.
4. Update the View.

---

# 5. Main Application

File:

```text
main.py
```

```python
import sys

from PyQt6.QtWidgets import QApplication

from models.user_model import UserModel
from views.main_view import MainView
from controllers.main_controller import MainController


def main():

    app = QApplication(sys.argv)

    # Create Model
    model = UserModel()

    # Create View
    view = MainView()

    # Create Controller
    controller = MainController(
        model,
        view
    )

    # Show UI
    view.show()

    sys.exit(app.exec())


if __name__ == "__main__":
    main()
```

---

# 6. Complete MVC Flow

Example:

```text
User enters name
        │
        ▼
QLineEdit
        │
        ▼
User clicks Save
        │
        ▼
QPushButton.clicked
        │
        ▼
Controller.save_name()
        │
        ├── Read name from View
        │
        ▼
Model.set_name()
        │
        ▼
Model stores data
        │
        ▼
Controller gets data
        │
        ▼
View QLabel updated
```

---

# 7. Signal and Slot Syntax

PyQt uses the **signal and slot** system.

Basic syntax:

```python
widget.signal.connect(function)
```

Example:

```python
self.button.clicked.connect(
    self.on_button_clicked
)
```

Function:

```python
def on_button_clicked(self):
    print("Button clicked")
```

With parameters:

```python
def update_name(self, name):
    print(name)
```

Example signal:

```python
self.input.textChanged.connect(
    self.update_name
)
```

---

# 8. Better MVC View Design

Instead of allowing the Controller to directly manipulate every widget, the View can provide methods.

Example:

```python
class MainView(QWidget):

    def __init__(self):
        super().__init__()

        self.name_input = QLineEdit()

        self.save_button = QPushButton("Save")

        self.result_label = QLabel()

    def get_name(self):

        return self.name_input.text()

    def set_result(self, text):

        self.result_label.setText(text)
```

Then the Controller becomes cleaner:

```python
class MainController:

    def __init__(self, model, view):

        self.model = model
        self.view = view

        self.view.save_button.clicked.connect(
            self.save_name
        )

    def save_name(self):

        name = self.view.get_name()

        self.model.set_name(name)

        self.view.set_result(
            self.model.get_name()
        )
```

This is generally a cleaner approach because the Controller does not need to know the details of every UI widget.

---

# 9. Example With Multiple Data

## Model

```python
class DeviceModel:

    def __init__(self):

        self.device_name = ""
        self.ip_address = ""

    def set_device(self, name, ip):

        self.device_name = name
        self.ip_address = ip

    def get_device(self):

        return {
            "name": self.device_name,
            "ip": self.ip_address
        }
```

---

## View

```python
from PyQt6.QtWidgets import (
    QWidget,
    QVBoxLayout,
    QLineEdit,
    QPushButton,
    QLabel
)


class DeviceView(QWidget):

    def __init__(self):

        super().__init__()

        layout = QVBoxLayout()

        self.name_input = QLineEdit()

        self.ip_input = QLineEdit()

        self.save_button = QPushButton(
            "Save Device"
        )

        self.result_label = QLabel()

        layout.addWidget(self.name_input)
        layout.addWidget(self.ip_input)
        layout.addWidget(self.save_button)
        layout.addWidget(self.result_label)

        self.setLayout(layout)

    def get_device_name(self):

        return self.name_input.text()

    def get_ip_address(self):

        return self.ip_input.text()

    def show_device(self, device):

        self.result_label.setText(
            f"Name: {device['name']}\n"
            f"IP: {device['ip']}"
        )
```

---

## Controller

```python
class DeviceController:

    def __init__(self, model, view):

        self.model = model
        self.view = view

        self.view.save_button.clicked.connect(
            self.save_device
        )

    def save_device(self):

        name = self.view.get_device_name()

        ip = self.view.get_ip_address()

        self.model.set_device(
            name,
            ip
        )

        device = self.model.get_device()

        self.view.show_device(device)
```

---

# 10. MVC With Database

For larger applications, the Model can communicate with a database.

Example:

```text
Controller
    │
    ▼
Model
    │
    ▼
Database
```

Example Model:

```python
class DeviceModel:

    def __init__(self, database):

        self.database = database

    def get_devices(self):

        return self.database.fetch_all(
            "SELECT * FROM devices"
        )

    def add_device(self, name, ip):

        query = """
        INSERT INTO devices
        (
            name,
            ip_address
        )
        VALUES
        (
            ?,
            ?
        )
        """

        self.database.execute(
            query,
            (
                name,
                ip
            )
        )
```

The Controller should call the Model:

```python
devices = self.model.get_devices()
```

Instead of directly accessing the database:

```python
# Avoid this in Controller

database.execute(
    "SELECT * FROM devices"
)
```

Preferred structure:

```text
View
 │
 ▼
Controller
 │
 ▼
Model
 │
 ▼
Database
```

---

# 11. Suggested Structure for Larger PyQt6 Application

For a larger application:

```text
pyqt6-app/
│
├── main.py
│
├── models/
│   ├── __init__.py
│   ├── device_model.py
│   ├── user_model.py
│   └── settings_model.py
│
├── views/
│   ├── __init__.py
│   ├── main_window.py
│   ├── device_view.py
│   ├── user_view.py
│   └── settings_view.py
│
├── controllers/
│   ├── __init__.py
│   ├── main_controller.py
│   ├── device_controller.py
│   ├── user_controller.py
│   └── settings_controller.py
│
├── services/
│   ├── __init__.py
│   ├── database_service.py
│   ├── api_service.py
│   └── mqtt_service.py
│
├── utils/
│   ├── __init__.py
│   └── helpers.py
│
└── resources/
    ├── icons/
    └── styles/
```

---

# 12. Recommended Architecture

For a real application, a structure like this is useful:

```text
┌──────────────┐
│     View     │
│              │
│  PyQt6 UI    │
└──────┬───────┘
       │
       │ Signals
       ▼
┌──────────────┐
│  Controller  │
│              │
│ Application  │
│    Logic     │
└──────┬───────┘
       │
       │
       ▼
┌──────────────┐
│    Model     │
│              │
│ Data / State │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Services   │
│              │
│ Database     │
│ API          │
│ MQTT         │
└──────────────┘
```

Recommended responsibilities:

| Layer | Responsibility |
|---|---|
| View | UI and user interaction |
| Controller | Application flow and event handling |
| Model | Application data and business logic |
| Service | Database, API, MQTT, files, external services |
| Utils | Helper functions |

---

# 13. Important MVC Rules

## View

The View should handle:

```text
- Buttons
- Inputs
- Labels
- Tables
- Dialogs
- Layouts
- Displaying data
```

Avoid putting database or business logic directly in the View.

Bad:

```python
def button_clicked(self):

    database.execute(
        "INSERT INTO devices ..."
    )
```

Better:

```python
def button_clicked(self):

    # Emit signal
    # Controller handles the action
    pass
```

---

## Controller

The Controller should:

```text
- Connect signals
- Handle user actions
- Validate input
- Call Model
- Call Services
- Update View
```

Example:

```python
def save_device(self):

    name = self.view.get_name()

    if not name:

        self.view.show_error(
            "Device name is required"
        )

        return

    self.model.save_device(name)

    self.view.show_success(
        "Device saved"
    )
```

---

## Model

The Model should manage application data.

Example:

```python
class DeviceModel:

    def __init__(self):

        self.devices = []

    def add_device(self, device):

        self.devices.append(device)

    def get_devices(self):

        return self.devices
```

---

# 14. Simple Application Startup Pattern

Recommended:

```python
def main():

    app = QApplication(sys.argv)

    model = DeviceModel()

    view = DeviceView()

    controller = DeviceController(
        model,
        view
    )

    view.show()

    sys.exit(
        app.exec()
    )
```

The Controller variable must remain referenced while the application runs.

---

# 15. Example Import Pattern

Absolute imports:

```python
from models.device_model import DeviceModel

from views.device_view import DeviceView

from controllers.device_controller import DeviceController
```

Example:

```text
main.py
   │
   ├── models/
   │     └── device_model.py
   │
   ├── views/
   │     └── device_view.py
   │
   └── controllers/
         └── device_controller.py
```

---

# 16. Minimal MVC Template

## Model

```python
class AppModel:

    def __init__(self):

        self.data = None

    def set_data(self, data):

        self.data = data

    def get_data(self):

        return self.data
```

## View

```python
from PyQt6.QtWidgets import (
    QWidget,
    QVBoxLayout,
    QPushButton,
    QLineEdit
)


class AppView(QWidget):

    def __init__(self):

        super().__init__()

        layout = QVBoxLayout()

        self.input = QLineEdit()

        self.button = QPushButton(
            "Submit"
        )

        layout.addWidget(
            self.input
        )

        layout.addWidget(
            self.button
        )

        self.setLayout(layout)

    def get_input(self):

        return self.input.text()
```

## Controller

```python
class AppController:

    def __init__(self, model, view):

        self.model = model

        self.view = view

        self.view.button.clicked.connect(
            self.handle_submit
        )

    def handle_submit(self):

        data = self.view.get_input()

        self.model.set_data(
            data
        )

        print(
            self.model.get_data()
        )
```

## Main

```python
import sys

from PyQt6.QtWidgets import QApplication

from models.app_model import AppModel

from views.app_view import AppView

from controllers.app_controller import AppController


def main():

    app = QApplication(sys.argv)

    model = AppModel()

    view = AppView()

    controller = AppController(
        model,
        view
    )

    view.show()

    sys.exit(
        app.exec()
    )


if __name__ == "__main__":

    main()
```

---

# Summary

Basic PyQt6 MVC structure:

```text
main.py
   │
   ├── Create Model
   │
   ├── Create View
   │
   └── Create Controller
            │
            ├── Connect View signals
            │
            ├── Read View data
            │
            ├── Update Model
            │
            └── Update View
```

The main idea is:

```text
View
  ↓
Controller
  ↓
Model
  ↓
Services / Database
```

A good rule for larger PyQt6 applications:

```text
UI code          -> views/
Application flow -> controllers/
Data logic       -> models/
External logic   -> services/
Helper functions -> utils/
```

This structure makes the application easier to maintain, test, and expand.