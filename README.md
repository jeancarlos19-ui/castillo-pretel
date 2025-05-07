from PySide6.QtWidgets import (
    QApplication, QMainWindow, QWidget, QVBoxLayout, QLabel,
    QLineEdit, QPushButton, QMessageBox, QMenuBar, QMenu,
    QDialog, QDialogButtonBox
)
from PySide6.QtGui import QIcon, QPixmap, QAction
from PySide6.QtCore import Qt
import sys

class CalculadoraPropina(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Calculadora de Propina")
        self.setMinimumWidth(300)
        self.setWindowIcon(QIcon("icono.png"))

        # Lista de historial de cálculos
        self.historial = []

        # Contenido principal
        self.widget_central = QWidget()
        self.layout = QVBoxLayout()

        # Imagen decorativa
        self.imagen = QLabel()
        pixmap = QPixmap("imagen.png")
        self.imagen.setPixmap(pixmap.scaledToWidth(200))
        self.imagen.setAlignment(Qt.AlignCenter)

        # Entradas de datos
        self.label_total = QLabel("Total de la cuenta:")
        self.input_total = QLineEdit()
        self.input_total.setPlaceholderText("Ej: 100.000")

        self.label_porcentaje = QLabel("Porcentaje de propina (%):")
        self.input_porcentaje = QLineEdit()
        self.input_porcentaje.setPlaceholderText("Ej: 15")

        self.label_personas = QLabel("Número de personas:")
        self.input_personas = QLineEdit()
        self.input_personas.setPlaceholderText("Ej: 2")

        self.boton_calcular = QPushButton("Calcular propina")
        self.boton_calcular.setIcon(QIcon("icono.png"))
        self.boton_calcular.clicked.connect(self.calcular_propina)

        # Agregar widgets al layout
        self.layout.addWidget(self.imagen)
        self.layout.addWidget(self.label_total)
        self.layout.addWidget(self.input_total)
        self.layout.addWidget(self.label_porcentaje)
        self.layout.addWidget(self.input_porcentaje)
        self.layout.addWidget(self.label_personas)
        self.layout.addWidget(self.input_personas)
        self.layout.addWidget(self.boton_calcular)

        self.widget_central.setLayout(self.layout)
        self.setCentralWidget(self.widget_central)

        # Crear menú
        self.crear_menu()

    def crear_menu(self):
        barra_menus = self.menuBar()

        menu_archivo = barra_menus.addMenu("Archivo")
        menu_ayuda = barra_menus.addMenu("Ayuda")
        menu_historial = barra_menus.addMenu("Historial")

        # Acciones del menú
        accion_salir = QAction(QIcon("icono_salir.png"), "Salir", self)
        accion_salir.triggered.connect(self.close)

        accion_acerca = QAction("Acerca de", self)
        accion_acerca.triggered.connect(self.mostrar_acerca)

        accion_ver_historial = QAction("Ver Historial", self)
        accion_ver_historial.triggered.connect(self.mostrar_historial)

        # Agregar acciones al menú
        menu_archivo.addAction(accion_salir)
        menu_ayuda.addAction(accion_acerca)
        menu_historial.addAction(accion_ver_historial)

    def mostrar_acerca(self):
        QMessageBox.information(
            self,
            "Acerca de",
            "Calculadora de Propina\nDesarrollado con PySide6.\n© 2025"
        )

    def calcular_propina(self):
        try:
            total = float(self.input_total.text())
            porcentaje = float(self.input_porcentaje.text())
            personas = int(self.input_personas.text())

            if personas <= 0:
                raise ValueError("El número de personas debe ser mayor que cero.")

            propina = total * (porcentaje / 100)
            total_final = total + propina
            por_persona = total_final / personas

            # Guardar en historial
            self.historial.append(
                f"Total: ${total:.2f} | Propina: ${propina:.2f} | Total con propina: ${total_final:.2f} | "
                f"Por persona ({personas}): ${por_persona:.2f}"
            )

            QMessageBox.information(
                self, "Resultado",
                f"Propina: ${propina:.2f}\nTotal con propina: ${total_final:.2f}\n"
                f"Cada persona paga: ${por_persona:.2f}"
            )
        except ValueError:
            QMessageBox.warning(self, "Error", "Por favor ingresa valores numéricos válidos.")

    def mostrar_historial(self):
        if not self.historial:
            QMessageBox.information(self, "Historial", "No hay cálculos anteriores.")
            return

        historial_dialogo = QDialog(self)
        historial_dialogo.setWindowTitle("Historial de Cálculos")
        layout = QVBoxLayout()

        for item in self.historial:
            layout.addWidget(QLabel(item))

        botones = QDialogButtonBox(QDialogButtonBox.Ok)
        botones.accepted.connect(historial_dialogo.accept)
        layout.addWidget(botones)

        historial_dialogo.setLayout(layout)
        historial_dialogo.exec()

if __name__ == "__main__":
    app = QApplication(sys.argv)
    ventana = CalculadoraPropina()
    ventana.show()
    sys.exit(app.exec())
