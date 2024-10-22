<?xml version="1.0" encoding="UTF-8"?>
<ui version="4.0">
 <class>MainWindow</class>
 <widget class="QMainWindow" name="MainWindow">
  <property name="geometry">
   <rect>
    <x>0</x>
    <y>0</y>
    <width>600</width>
    <height>400</height>
   </rect>
  </property>
  <property name="windowTitle">
   <string>Clip-Clap</string>
  </property>
  <widget class="QWidget" name="centralwidget">
   <layout class="QVBoxLayout" name="verticalLayout">
    <item>
     <layout class="QHBoxLayout" name="topButtonsLayout">
      <item>
       <widget class="QPushButton" name="browseButton">
        <property name="text">
         <string>Przeglądaj katalog</string>
        </property>
       </widget>
      </item>
      <item>
       <widget class="QPushButton" name="saveButton">
        <property name="text">
         <string>Zapisz TXT</string>
        </property>
       </widget>
      </item>
     </layout>
    </item>
    <item>
     <widget class="QListWidget" name="fileList">
      <property name="selectionMode">
       <enum>QAbstractItemView::SingleSelection</enum>
      </property>
     </widget>
    </item>
    <item>
     <layout class="QHBoxLayout" name="bottomButtonsLayout">
      <item>
       <widget class="QPushButton" name="useButton">
        <property name="text">
         <string>Użyj/Kopiuj</string>
        </property>
        <property name="enabled">
         <bool>false</bool>
        </property>
       </widget>
      </item>
      <item>
       <widget class="QPushButton" name="openFolderButton">
        <property name="text">
         <string>Otwórz katalog</string>
        </property>
        <property name="enabled">
         <bool>false</bool>
        </property>
       </widget>
      </item>
      <item>
       <widget class="QPushButton" name="deleteButton">
        <property name="text">
         <string>Usuń z listy</string>
        </property>
        <property name="enabled">
         <bool>false</bool>
        </property>
       </widget>
      </item>
     </layout>
    </item>
   </layout>
  </widget>
  <action name="actionQuit">
   <property name="text">
    <string>Quit</string>
   </property>
  </action>
 </widget>
 <resources/>
 <connections/>
</ui>
import sys
import os
from PyQt5.QtWidgets import QApplication, QMainWindow, QFileDialog, QListWidgetItem, QMessageBox
from PyQt5.QtGui import QIcon
from PyQt5.QtCore import Qt
from pathlib import Path
import shutil

# Zakładamy, że plik interfejsu wygenerowany przez pyuic5 nazywa się 'ui_clipclap.py'
from ui_clipclap import Ui_MainWindow  # Import interfejsu z Qt Designer

class ClipClapApp(QMainWindow, Ui_MainWindow):
    def __init__(self):
        super().__init__()
        self.setupUi(self)
        
        # Podpięcie przycisków do funkcji
        self.browseButton.clicked.connect(self.browse_folder)
        self.saveButton.clicked.connect(self.save_txt)
        self.useButton.clicked.connect(self.copy_file)
        self.openFolderButton.clicked.connect(self.open_folder)
        self.deleteButton.clicked.connect(self.remove_file)

        # Wstępne ustawienie przycisków manipulacyjnych jako nieaktywne
        self.fileList.itemSelectionChanged.connect(self.update_buttons)

        # Przechowywanie pełnych ścieżek do plików
        self.file_paths = []

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
        # Zapisuje listę plików do pliku TXT
        if not self.file_paths:
            QMessageBox.warning(self, "Brak plików", "Brak plików do zapisania!")
            return

        save_path, _ = QFileDialog.getSaveFileName(self, "Zapisz plik TXT", "", "Pliki TXT (*.txt)")
        if save_path:
            with open(save_path, 'w') as file:
                for file_path in self.file_paths:
                    file.write(str(file_path) + '\n')
            QMessageBox.information(self, "Zapisano", "Lista plików została zapisana.")

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
