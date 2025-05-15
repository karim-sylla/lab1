# Aplikacje desktopowe (Tkinter i PySide6)

Aplikacje desktopowe oferują przyjazny dla użytkownika interfejs graficzny, ułatwiając interakcję z modelem językowym. W tym module zaimplementujemy dwie wersje aplikacji desktopowej przy użyciu różnych bibliotek GUI: Tkinter i PySide6.

## Porównanie Tkinter i PySide6

| Cecha | Tkinter | PySide6 |
|-------|---------|---------|
| Instalacja | Wbudowana w Pythona | Wymaga osobnej instalacji |
| Wygląd | Prosty, natywny dla każdego OS | Nowoczesny, jednolity na różnych platformach |
| Złożoność | Niska, łatwy start | Średnia, więcej możliwości |
| Dokumentacja | Dobra | Bardzo dobra (część ekosystemu Qt) |
| Wydajność | Dobra dla prostych aplikacji | Lepsza dla złożonych aplikacji |
| Stylizacja | Podstawowa | Zaawansowana (CSS-podobna) |

## Przygotowanie środowiska

1. **Tkinter** jest już wbudowany w standardową bibliotekę Pythona, więc nie wymaga dodatkowej instalacji.

2. **Dla PySide6 - aktualizacja pliku requirements.txt**
   ```bash
   echo "pyside6" >> requirements.txt
   pip install -r requirements.txt
   ```

3. **Utworzenie wspólnego modułu API** (jeśli jeszcze nie istnieje)
   ```bash
   # Implementuj gemini_api.py zgodnie z poprzednim modułem
   git add gemini_api.py
   git commit -m "Dodanie modułu API Gemini"
   ```

## Aplikacja Tkinter (tkinter_app.py)

Tkinter to standardowa biblioteka do tworzenia interfejsów graficznych w Pythonie. Jest dostępna w każdej instalacji Pythona bez potrzeby instalowania dodatkowych pakietów, co czyni ją doskonałym wyborem dla prostych aplikacji i szybkiego prototypowania.

Zamiast ręcznego pisania kodu, możesz wykorzystać GitHub Copilot. Po zainstalowaniu rozszerzenia w VSC, umieść poniższy prompt i pozwól Copilotowi wygenerować kod:

```
Stwórz aplikację Tkinter do interakcji z Gemini API. Dodaj:
- dwa suwaki: temperatura (0.0-1.0) i maksymalna liczba tokenów (100-2048)
- pole tekstowe na instrukcję systemową
- pole tekstowe na zapytanie użytkownika
- przycisk wysyłania
- pole tekstowe na wyświetlenie odpowiedzi
Użyj funkcji generate_response() z gemini_api.py
```

Przykładowy kod Tkinter:

```python
import tkinter as tk
from tkinter import ttk
from gemini_api import generate_response

def create_tkinter_app():
    def on_generate():
        # Pobieranie wartości z interfejsu
        prompt = prompt_text.get("1.0", tk.END).strip()
        system = system_text.get("1.0", tk.END).strip()
        temperature = temp_slider.get()
        max_tokens = int(token_slider.get())
        
        if prompt:
            # Wyświetlenie informacji o generowaniu
            result_text.delete("1.0", tk.END)
            result_text.insert("1.0", "Generowanie odpowiedzi...")
            root.update()
            
            # Generowanie odpowiedzi
            response = generate_response(
                prompt=prompt,
                system_prompt=system,
                temperature=temperature,
                max_tokens=max_tokens
            )
            
            # Wyświetlenie odpowiedzi
            result_text.delete("1.0", tk.END)
            result_text.insert("1.0", response)
        else:
            result_text.delete("1.0", tk.END)
            result_text.insert("1.0", "Proszę wprowadzić zapytanie.")
    
    # Utworzenie głównego okna
    root = tk.Tk()
    root.title("Gemini API - Aplikacja Tkinter")
    root.geometry("800x600")
    
    # Tworzenie kontrolek
    frame = ttk.Frame(root, padding="10")
    frame.pack(fill=tk.BOTH, expand=True)
    
    # Suwak temperatury
    ttk.Label(frame, text="Temperatura:").grid(row=0, column=0, sticky=tk.W)
    temp_slider = ttk.Scale(frame, from_=0.0, to=1.0, orient=tk.HORIZONTAL, length=200)
    temp_slider.set(0.7)
    temp_slider.grid(row=0, column=1, sticky=tk.W)
    temp_value = ttk.Label(frame, text="0.7")
    temp_value.grid(row=0, column=2, sticky=tk.W)
    
    # Aktualizacja etykiety temperatury
    def update_temp_label(event):
        temp_value.config(text=f"{temp_slider.get():.1f}")
    
    temp_slider.bind("<Motion>", update_temp_label)
    
    # Suwak liczby tokenów
    ttk.Label(frame, text="Maksymalna liczba tokenów:").grid(row=1, column=0, sticky=tk.W)
    token_slider = ttk.Scale(frame, from_=100, to=2048, orient=tk.HORIZONTAL, length=200)
    token_slider.set(1024)
    token_slider.grid(row=1, column=1, sticky=tk.W)
    token_value = ttk.Label(frame, text="1024")
    token_value.grid(row=1, column=2, sticky=tk.W)
    
    # Aktualizacja etykiety tokenów
    def update_token_label(event):
        token_value.config(text=str(int(token_slider.get())))
    
    token_slider.bind("<Motion>", update_token_label)
    
    # Pole na wpisanie tekstu systemowego
    ttk.Label(frame, text="Tekst systemowy:").grid(row=2, column=0, sticky=tk.W)
    system_text = tk.Text(frame, height=3, width=70)
    system_text.grid(row=2, column=1, columnspan=2, pady=5)
    system_text.insert("1.0", "Jesteś pomocnym asystentem AI.")
    
    # Pole na wpisanie zapytania
    ttk.Label(frame, text="Zapytanie:").grid(row=3, column=0, sticky=tk.W)
    prompt_text = tk.Text(frame, height=5, width=70)
    prompt_text.grid(row=3, column=1, columnspan=2, pady=5)
    
    # Przycisk zatwierdzenia
    generate_button = ttk.Button(frame, text="Generuj odpowiedź", command=on_generate)
    generate_button.grid(row=4, column=1, pady=10)
    
    # Pole z wynikiem od LLM
    ttk.Label(frame, text="Odpowiedź:").grid(row=5, column=0, sticky=tk.NW)
    result_text = tk.Text(frame, height=15, width=70)
    result_text.grid(row=5, column=1, columnspan=2, pady=5)
    
    return root

if __name__ == "__main__":
    app = create_tkinter_app()
    app.mainloop()
```

Po zaimplementowaniu aplikacji Tkinter, zapisz zmiany w repozytorium:

```bash
git add tkinter_app.py
git commit -m "Implementacja aplikacji desktopowej Tkinter"
```

## Aplikacja PySide6 (pyside_app.py)

PySide6 jest oficjalnym wiązaniem Pythona dla Qt, popularnego frameworka do tworzenia aplikacji wieloplatformowych. W porównaniu do Tkinter, PySide6 oferuje bardziej nowoczesny wygląd, zaawansowane komponenty i lepszą obsługę stylów. Jest idealny do tworzenia profesjonalnych aplikacji desktopowych.

Podobnie, możesz wykorzystać GitHub Copilot do wygenerowania kodu w PySide6:

```
Utwórz aplikację PySide6 do komunikacji z Gemini API zawierającą:
- suwak temperatury (0.0-1.0)
- suwak maksymalnej liczby tokenów (100-2048)
- pole do wprowadzenia instrukcji systemowej
- pole do wprowadzenia zapytania użytkownika
- przycisk generowania odpowiedzi
- pole wynikowe
Wykorzystaj funkcję generate_response() z gemini_api.py
```

Przykładowy kod PySide6:

```python
import sys
from PySide6.QtWidgets import (QApplication, QMainWindow, QWidget, QVBoxLayout, 
                             QHBoxLayout, QLabel, QSlider, QTextEdit, QPushButton)
from PySide6.QtCore import Qt
from gemini_api import generate_response

class GeminiApp(QMainWindow):
    def __init__(self):
        super().__init__()
        # Konfiguracja okna głównego
        self.setWindowTitle("Gemini API - Aplikacja PySide6")
        self.setMinimumSize(800, 600)
        
        # Utworzenie interfejsu
        self.setup_ui()
    
    def setup_ui(self):
        # Kontener główny
        central_widget = QWidget()
        main_layout = QVBoxLayout(central_widget)
        self.setCentralWidget(central_widget)
        
        # Suwak temperatury
        temp_layout = QHBoxLayout()
        temp_label = QLabel("Temperatura:")
        self.temp_slider = QSlider(Qt.Horizontal)
        self.temp_slider.setRange(0, 100)  # 0-100 dla wartości 0.0-1.0
        self.temp_slider.setValue(70)      # Domyślnie 0.7
        self.temp_value = QLabel("0.7")
        self.temp_slider.valueChanged.connect(self.update_temp_label)
        
        temp_layout.addWidget(temp_label)
        temp_layout.addWidget(self.temp_slider)
        temp_layout.addWidget(self.temp_value)
        main_layout.addLayout(temp_layout)
        
        # Suwak liczby tokenów
        tokens_layout = QHBoxLayout()
        tokens_label = QLabel("Maksymalna liczba tokenów:")
        self.tokens_slider = QSlider(Qt.Horizontal)
        self.tokens_slider.setRange(100, 2048)
        self.tokens_slider.setValue(1024)  # Domyślnie 1024
        self.tokens_value = QLabel("1024")
        self.tokens_slider.valueChanged.connect(self.update_tokens_label)
        
        tokens_layout.addWidget(tokens_label)
        tokens_layout.addWidget(self.tokens_slider)
        tokens_layout.addWidget(self.tokens_value)
        main_layout.addLayout(tokens_layout)
        
        # Pole na tekst systemowy
        main_layout.addWidget(QLabel("Tekst systemowy:"))
        self.system_text = QTextEdit()
        self.system_text.setPlainText("Jesteś pomocnym asystentem AI.")
        self.system_text.setMaximumHeight(80)
        main_layout.addWidget(self.system_text)
        
        # Pole na zapytanie
        main_layout.addWidget(QLabel("Zapytanie:"))
        self.prompt_text = QTextEdit()
        self.prompt_text.setMaximumHeight(120)
        main_layout.addWidget(self.prompt_text)
        
        # Przycisk zatwierdzenia
        self.generate_button = QPushButton("Generuj odpowiedź")
        self.generate_button.clicked.connect(self.generate_response)
        main_layout.addWidget(self.generate_button)
        
        # Pole z wynikiem
        main_layout.addWidget(QLabel("Odpowiedź:"))
        self.result_text = QTextEdit()
        self.result_text.setReadOnly(True)
        main_layout.addWidget(self.result_text)
    
    def update_temp_label(self, value):
        # Konwersja 0-100 na 0.0-1.0
        decimal_value = value / 100
        self.temp_value.setText(f"{decimal_value:.1f}")
    
    def update_tokens_label(self, value):
        self.tokens_value.setText(str(value))
    
    def generate_response(self):
        prompt = self.prompt_text.toPlainText()
        system_prompt = self.system_text.toPlainText()
        temperature = self.temp_slider.value() / 100  # Konwersja 0-100 na 0.0-1.0
        max_tokens = self.tokens_slider.value()
        
        if prompt:
            self.result_text.setPlainText("Generowanie odpowiedzi...")
            QApplication.processEvents()  # Aktualizacja UI
            
            response_text = generate_response(
                prompt=prompt,
                system_prompt=system_prompt,
                temperature=temperature,
                max_tokens=max_tokens
            )
            
            self.result_text.setPlainText(response_text)
        else:
            self.result_text.setPlainText("Proszę wprowadzić zapytanie.")

def create_pyside_app():
    app = QApplication(sys.argv)
    window = GeminiApp()
    window.show()
    return app, window

if __name__ == "__main__":
    app, window = create_pyside_app()
    sys.exit(app.exec())
```

Po zaimplementowaniu aplikacji PySide6, zapisz zmiany w repozytorium:

```bash
git add pyside_app.py
git commit -m "Implementacja aplikacji desktopowej PySide6"
```

## Uruchamianie aplikacji

Aby uruchomić aplikację Tkinter:
```bash
python tkinter_app.py
```

Aby uruchomić aplikację PySide6:
```bash
python pyside_app.py
```

## Różnice między Tkinter i PySide6

| Cecha | Tkinter | PySide6 |
|-------|---------|---------|
| Biblioteka | Wbudowana w Pythona | Wymaga instalacji |
| Wygląd | Prosty, natywny dla OS | Nowoczesny, jednolity |
| Elastyczność | Podstawowa | Zaawansowana |
| Złożoność | Niska | Średnia |
| Dokumentacja | Dobra | Bardzo dobra |

Obie aplikacje mają identyczną funkcjonalność, ale różnią się strukturą kodu i stylem interfejsu.