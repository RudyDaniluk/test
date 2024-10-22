import sys
import os
from PyQt5.QtWidgets import QApplication, QMainWindow, QFileDialog, QListWidgetItem, QMessageBox
from PyQt5.QtGui import QIcon
from PyQt5 import uic
from PyQt5.QtCore import Qt
from pathlib import Path
import shutil

class ClipClapApp(QMainWindow):
    def __init__(self):
        super().__init__()

        # Ładowanie pliku UI bezpośrednio
        uic.loadUi('untitled.ui', self)

        # Łączenie elementów interfejsu z kodem
        self.browseButton = self.findChild(QPushButton, 'browseButton')
        self.saveButton = self.findChild(QPushButton, 'saveButton')
        self.fileList = self.findChild(QListWidget, 'fileList')
        self.useButton = self.findChild(QPushButton, 'useButton')
        self.openFolderButton = self.findChild(QPushButton, 'openFolderButton')
        self.deleteButton = self.findChild(QPushButton, 'deleteButton')

        # Wstępne ustawienia dla przeciągnij-upuść
        self.fileList.setAcceptDrops(True)
        self.fileList.dragEnterEvent = self.dragEnterEvent
        self.fileList.dropEvent = self.dropEvent

        # Podpięcie przycisków do funkcji
        self.browseButton.clicked.connect(self.browse_folder)
        self.saveButton.clicked.connect(self.save_txt)
        self.useButton.clicked.connect(self.copy_file)
        self.openFolderButton.clicked.connect(self.open_folder)
        self.deleteButton.clicked.connect(self.remove_file)

        # Wstępne ustawienie przycisków manipulacyjnych jako nieaktywne
        self.fileList.itemSelectionChanged.connect(self.update_buttons)
        self.useButton.setEnabled(False)
        self.openFolderButton.setEnabled(False)
        self.deleteButton.setEnabled(False)

        # Przechowywanie pełnych ścieżek do plików
        self.file_paths = []

    # Funkcja przeciągnij i upuść - przeciąganie plików
    def dragEnterEvent(self, event):
        if event.mimeData().hasUrls():
            event.accept()
        else:
            event.ignore()

    # Funkcja przeciągnij i upuść - upuszczanie plików
    def dropEvent(self, event):
        urls = event.mimeData().urls()
        for url in urls:
            file_path = url.toLocalFile()
            file = Path(file_path)
            if file.is_file():
                item = QListWidgetItem(file.name)
                item.setIcon(QIcon("icon.png"))  # Możesz dodać własną ikonę
                self.fileList.addItem(item)
                self.file_paths.append(file)

    def browse_folder(self):
        # Otwiera okno dialogowe, aby wybrać katalog
        folder_path = QFileDialog.getExistingDirectory(self, "Wybierz katalog")
        if folder_path:
            folder = Path(folder_path)
            # Dodajemy wszystkie pliki z katalogu do listy
            for file in folder.iterdir():
                if file.is_file():
                    item = QListWidgetItem(file.name)
                    item.setIcon(QIcon("icon.png"))  # Możesz dostosować ikonę
                    self.fileList.addItem(item)
                    self.file_paths.append(file)

    def save_txt(self):
        # Zapisuje listę plików do pliku TXT w wybranym katalogu
        if not self.file_paths:
            QMessageBox.warning(self, "Brak plików", "Brak plików do zapisania!")
            return

        # Wybór katalogu, w którym zapisany zostanie plik TXT
        save_dir = QFileDialog.getExistingDirectory(self, "Wybierz katalog do zapisania pliku TXT")
        if save_dir:
            save_path = Path(save_dir) / "file_list.txt"
            with open(save_path, 'w') as file:
                for file_path in self.file_paths:
                    file.write(str(file_path) + '\n')
            QMessageBox.information(self, "Zapisano", f"Lista plików została zapisana w {save_path}")

    def update_buttons(self):
        # Aktywacja/dezaktywacja przycisków na podstawie zaznaczenia
        selected_items = self.fileList.selectedItems()
        if selected_items:
            self.useButton.setEnabled(True)
            self.openFolderButton.setEnabled(True)
            self.deleteButton.setEnabled(True)
        else:
            self.useButton.setEnabled(False)
            self.openFolderButton.setEnabled(False)
            self.deleteButton.setEnabled(False)

    def copy_file(self):
        # Kopiuje zaznaczony plik do schowka
        selected_items = self.fileList.selectedItems()
        if selected_items:
            selected_index = self.fileList.row(selected_items[0])
            selected_file = self.file_paths[selected_index]
            clipboard = QApplication.clipboard()
            clipboard.setText(str(selected_file))
            QMessageBox.information(self, "Skopiowano", f"Skopiowano plik: {selected_file}")

    def open_folder(self):
        # Otwiera katalog, w którym znajduje się zaznaczony plik
        selected_items = self.fileList.selectedItems()
        if selected_items:
            selected_index = self.fileList.row(selected_items[0])
            selected_file = self.file_paths[selected_index]
            folder_path = str(Path(selected_file).parent)
            os.startfile(folder_path)

    def remove_file(self):
        # Usuwa zaznaczony plik z listy
        selected_items = self.fileList.selectedItems()
        if selected_items:
            selected_index = self.fileList.row(selected_items[0])
            self.fileList.takeItem(selected_index)
            del self.file_paths[selected_index]

def main():
    app = QApplication(sys.argv)
    window = ClipClapApp()
    window.show()
    sys.exit(app.exec_())

if __name__ == "__main__":
    main()
