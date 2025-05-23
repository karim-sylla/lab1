# Wprowadzenie i aplikacja terminalowa

## Cel ćwiczenia

Celem tego projektu jest zdobycie praktycznych umiejętności budowania aplikacji wykorzystujących modele językowe (LLM) na przykładzie Gemini API. Przejdziemy przez pełny cykl rozwoju - od prostej aplikacji terminalowej, przez aplikacje desktopowe, aż po nowoczesne interfejsy webowe. Poznasz różne metody integracji API modeli językowych z aplikacjami oraz nauczysz się budować przyjazne dla użytkownika interfejsy do interakcji z AI. Te umiejętności stanowią solidną podstawę do tworzenia zaawansowanych narzędzi i rozwiązań opartych na sztucznej inteligencji.

## Przygotowanie środowiska

1. **Stworzenie folderu projektu**
   ```bash
   mkdir gemini_projekt
   cd gemini_projekt
   ```

2. **Instalacja GitHub Copilot w VSC**
   - Otwórz VSC
   - Przejdź do Extensions (Ctrl+Shift+X)
   - Wyszukaj "GitHub Copilot" i zainstaluj

3. **Założenie konta Gemini Studio**
   - Odwiedź https://aistudio.google.com/app/prompts/new_chat
   - Zaloguj się kontem Google
   - Przejdź do sekcji API Keys (https://aistudio.google.com/app/apikey)
   - Wygeneruj nowy klucz API

4. **Testowanie API w terminalu (curl)**
   ```bash
   curl "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=YOUR_API_KEY" \
     -H 'Content-Type: application/json' \
     -X POST \
     -d '{
       "contents": [
         {
           "parts": [
             {
               "text": "Wytłumacz jak działa LLM w kilku słowach?"
             }
           ]
         }
       ]
     }'
   ```
   
   > Uwaga: Na Windows polecenie curl należy uruchomić w Git Bash (a nie w domyślnym PowerShell). W Visual Studio Code można łatwo otworzyć terminal Git Bash wybierając go z rozwijanej listy w oknie terminala.
   
   > Bezpośrednie testowanie API przez terminal pozwala na szybką weryfikację działania interfejsu bez pisania kodu. To dobry sposób na zrozumienie struktury żądań i odpowiedzi.

5. **Utworzenie i aktywacja virtual env**
   ```bash
   python -m venv venv
   # Windows
   venv\Scripts\activate
   # Linux/Mac
   source venv/bin/activate
   ```
   
   > Wirtualne środowisko (virtual environment) pozwala na izolację zależności projektu od innych projektów Pythona na tym samym komputerze. Zapewnia to spójność wersji bibliotek i eliminuje konflikty między różnymi projektami, co jest kluczowe dla stabilności aplikacji.

6. **Utworzenie pliku .env**
   ```
   GEMINI_API_KEY=TU_WSTAW_SWÓJ_KLUCZ_API
   ```
   
   > Plik .env służy do przechowywania zmiennych środowiskowych, takich jak klucze API, które nie powinny być umieszczane bezpośrednio w kodzie. To praktyka zwiększająca bezpieczeństwo aplikacji, ponieważ wrażliwe dane nie są przechowywane w repozytorium kodu.

7. **Utworzenie pliku requirements.txt**
   ```
   python-dotenv
   google-genai
   ```
   
   > Na tym etapie potrzebujemy tylko tych dwóch pakietów do pracy z API Gemini. W kolejnych fazach będziemy dodawać inne zależności odpowiednio do potrzeb.

8. **Utworzenie pliku .gitignore**
   Koniecznie utwórz  plik `.gitignore`:
   
   - Utwórz nowy plik tekstowy o nazwie `.gitignore` w głównym katalogu projektu
   - Dodaj do niego następujące linie:
     ```
     venv/
     .env
     __pycache__/
     *.pyc
     ```
   
   
   > Plik .gitignore określa, które pliki i katalogi Git powinien ignorować podczas śledzenia zmian. Zwykle pomijamy pliki tymczasowe, katalogi środowiska wirtualnego, pliki zawierające wrażliwe dane (jak .env) oraz pliki specyficzne dla IDE, ponieważ nie są one istotne dla projektu i mogą zawierać dane specyficzne dla danego komputera.

9. **Weryfikacja instalacji Git i aktywacja**
   Po utworzeniu pliku `.gitignore`, możemy przystąpić do inicjalizacji repozytorium Git. Najpierw warto sprawdzić, czy Git jest poprawnie zainstalowany, a następnie skonfigurować podstawowe informacje o użytkowniku. Te kroki są niezbędne przed rozpoczęciem śledzenia zmian w projekcie.
   
   ```bash
   git --version
   git config --global user.name "Karim Sylla"
   git config --global user.email "karim.sylla@gmail.com"
   git init
   git add .
   git commit -m "Inicjalizacja projektu"
   ```

   > Git pozwala na śledzenie zmian w projekcie, wersjonowanie kodu i łatwą współpracę. Konfiguracja nazwy użytkownika i adresu email jest wymagana przed pierwszym commitem, by właściwie identyfikować autora zmian.

10. **Instalacja zależności**
   Po inicjalizacji repozytorium Git, następnym krokiem jest instalacja wszystkich wymaganych bibliotek Python zdefiniowanych w pliku requirements.txt. Jest to niezbędne, aby nasza aplikacja miała dostęp do wszystkich potrzebnych narzędzi i interfejsów API. Wykonaj polecie w terminalu
   
   ```bash
   pip install -r requirements.txt
   ```
   
   > Instalacja zależności z pliku requirements.txt zapewnia, że wszystkie wymagane biblioteki będą dostępne w odpowiednich wersjach. To ważny krok pozwalający na utrzymanie spójności środowiska i uniknięcie problemów z kompatybilnością między różnymi wersjami bibliotek.

11. **Dokumentacja Gemini API**
   Przed rozpoczęciem programowania warto zapoznać się z oficjalną dokumentacją Gemini API, która zawiera szczegółowe informacje o dostępnych funkcjach, parametrach i przykładach użycia:
   
   - Przewodnik szybkiego startu: https://ai.google.dev/gemini-api/docs/quickstart
   - Dokumentacja generowania tekstu: https://ai.google.dev/gemini-api/docs/text-generation
   
   > Dokumentacja API jest podstawowym źródłem wiedzy dla programisty. Zawiera szczegółowe informacje o wszystkich dostępnych funkcjach, ich parametrach, wartościach zwracanych oraz przykładach użycia. Regularne odwoływanie się do dokumentacji pomaga w efektywnym wykorzystaniu możliwości API i uniknięciu typowych błędów.

## Aplikacja terminalowa (app_terminal.py)

Aplikacje terminalowe są doskonałym punktem wyjścia do testowania API bez konieczności budowania złożonego interfejsu użytkownika. Pozwalają na szybkie eksperymenty i weryfikację działania API przed przejściem do bardziej złożonych implementacji. Poniższy kod stanowi najprostszy przykład komunikacji z API Gemini.

W najprostszej wersji, aplikacja terminalowa jest prostym skryptem bezpośrednio komunikującym się z API:

```
Utwórz skrypt terminalowy, który wczytuje GEMINI_API_KEY z .env, 
inicjalizuje klienta Gemini i wysyła zapytanie do modelu gemini-2.0-flash
```

```python
import os
from dotenv import load_dotenv
from google import genai

# Załadowanie zmiennych środowiskowych z pliku .env
load_dotenv()

# Pobranie klucza API z zmiennych środowiskowych
api_key = os.getenv("GEMINI_API_KEY")

# Inicjalizacja klienta Gemini
client = genai.Client(api_key=api_key)

# Generowanie odpowiedzi
response = client.models.generate_content(
    model="gemini-2.0-flash", 
    contents="Explain how AI works in a few words"
)

# Wyświetlenie odpowiedzi
print(response.text)
```

Po zakończeniu implementacji aplikacji terminalowej, zapisz zmiany w repozytorium:

```bash
git add app_terminal.py
git commit -m "Implementacja prostej aplikacji terminalowej z Gemini API"
```

## Struktura projektu na tym etapie

```
gemini_projekt/
├── gemini_api.py       # Moduł z funkcją API
├── app_terminal.py     # Prosta aplikacja terminalowa
├── .env                # Plik z kluczem API
└── requirements.txt    # Wymagane zależności
```

## Co dalej?

Następnym krokiem będzie utworzenie modułu API Gemini, który posłuży jako wspólna warstwa komunikacji z API dla wszystkich naszych aplikacji. Przejdź do [modułu API](gemini-api-module.md), aby poznać szczegóły implementacji funkcji obsługującej Gemini API.

Po opanowaniu podstaw komunikacji z API, zajmiemy się budowaniem aplikacji desktopowych, które zapewnią bardziej przyjazny interfejs użytkownika do interakcji z modelem Gemini. Przejdź do [aplikacji desktopowych](aplikacje-desktopowe.md), aby dowiedzieć się, jak tworzyć aplikacje GUI przy użyciu Tkinter i PySide6.