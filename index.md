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
   - Wyszukaj "GitHub Copilot" i zainstaluj + zaloguj

3. **Weryfikacja i instalacja Pythona (oficjalne źródło)**
    - Sprawdź, czy Python jest zainstalowany oraz jaką ma wersję.

       Windows (PowerShell):
       ```powershell
       python --version
       py --version
       ```

       Jeśli polecenia nie działają lub wersja jest starsza niż 3.11, zainstaluj Pythona z oficjalnej strony.

    - Instalacja dla Windows z python.org:
       1) Wejdź na https://www.python.org/downloads/ i pobierz instalator dla Windows (64-bit) w wersji 3.11.x lub 3.12.x (rekomendowane).
       2) Uruchom instalator, zaznacz opcję „Add Python to PATH”.
       3) Opcjonalnie wybierz „Customize installation”. Uwaga: „Install for all users” wymaga uprawnień administratora.
       4) Po instalacji otwórz nowy terminal i zweryfikuj:

       ```powershell
       python --version
       py -V
       ```

   - Uprawnienia — co działa bez administratora?
       - Bez admina (zalecane w środowiskach studenckich/firmowych):
          - „Install Now” (instalacja dla bieżącego użytkownika do AppData) — działa bez admina.
          - Pozostaw „Add Python to PATH” zaznaczone (dodaje do PATH użytkownika).
          - Nie zaznaczaj „Install for all users” ani „Install launcher for all users”.
       - Wymaga uprawnień administratora:
          - „Install for all users” (instalacja do Program Files, PATH systemowy).


4. **Założenie konta Gemini Studio**
   - Odwiedź https://aistudio.google.com/app/prompts/new_chat
   - Zaloguj się kontem Google
   - Przejdź do sekcji API Keys (https://aistudio.google.com/app/apikey)
   - Wygeneruj nowy klucz API

   Obowiązuje limity darmowego wykorzystania modeli Gemini
   - Gemini 2.5 Flash: 10 RPM, 250k TPM, 250 RPD
   - Gemini 2.5 Pro: 5 RPM, 250k TPM, 100 RPD
   - Gemini 2.0 Flash: 15 RPM, 1,000k TPM, 200 RPD

   Skróty: RPM — żądania na minutę; TPM — tokeny (wejściowe) na minutę; RPD — żądania na dzień.
   ([dŹródło: ](https://ai.google.dev/gemini-api/docs/rate-limits))

5. **Testowanie API w terminalu (curl)**
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

6. **Utworzenie i aktywacja virtual env**
   ```bash
   python -m venv venv
   # Windows
   venv\Scripts\activate

   ```
   Wskazówka:
   - Jeśli masz kilka wersji, możesz wybrać konkretną przy tworzeniu venv, np.: `py -3.12 -m venv venv` lub `py -3.11 -m venv venv`.

   > Wirtualne środowisko (virtual environment) pozwala na izolację zależności projektu od innych projektów Pythona na tym samym komputerze. Zapewnia to spójność wersji bibliotek i eliminuje konflikty między różnymi projektami, co jest kluczowe dla stabilności aplikacji.

   **Obsługa błędu aktywacji venv na Windows:**

   Jeśli podczas aktywacji środowiska poleceniem `.\venv\Scripts\Activate` pojawi się błąd dotyczący polityki wykonywania skryptów (np. "execution of scripts is disabled"), możesz jednorazowo zmienić politykę w PowerShell:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
   ```

   To pozwala na uruchamianie skryptów lokalnych, ale wymaga podpisania skryptów pobranych z internetu.

   Aby wyłączyć środowisko, wpisz:

   ```bash
   deactivate
   ```
7. **Utworzenie pliku .env**
   ```
   GEMINI_API_KEY=TU_WSTAW_SWÓJ_KLUCZ_API
   ```
   
   > Plik .env służy do przechowywania zmiennych środowiskowych, takich jak klucze API, które nie powinny być umieszczane bezpośrednio w kodzie. To praktyka zwiększająca bezpieczeństwo aplikacji, ponieważ wrażliwe dane nie są przechowywane w repozytorium kodu.

8. **Utworzenie pliku requirements.txt**
   ```
   python-dotenv
   google-genai
   ```
   
   > Na tym etapie potrzebujemy tylko tych dwóch pakietów do pracy z API Gemini. W kolejnych fazach będziemy dodawać inne zależności odpowiednio do potrzeb.

   Krótkie omówienie:
   - Do czego służy: requirements.txt przechowuje listę zależności projektu (często z wersjami), aby każdy mógł odtworzyć środowisko poleceniem: `pip install -r requirements.txt`.
   - Prostsza alternatywa: zainstaluj pakiety ręcznie w aktywnym venv: `pip install python-dotenv google-genai`. W razie potrzeby zapisz stan do pliku: `pip freeze > requirements.txt`.
      - Wprowadzenie  pliku requirments: https://www.danesawszedzie.pl/jak-w-pythonie-przekazac-uzyte-biblioteki-czyli-requirements-txt/
9. **Instalacja zależności**
   Następnym krokiem jest instalacja wszystkich wymaganych bibliotek Python zdefiniowanych w pliku requirements.txt. Jest to niezbędne, aby nasza aplikacja miała dostęp do wszystkich potrzebnych narzędzi i interfejsów API. Wykonaj polecie w terminalu
   
   ```bash
   pip install -r requirements.txt
   ```
   
   > Instalacja zależności z pliku requirements.txt zapewnia, że wszystkie wymagane biblioteki będą dostępne w odpowiednich wersjach. To ważny krok pozwalający na utrzymanie spójności środowiska i uniknięcie problemów z kompatybilnością między różnymi wersjami bibliotek.

Punkty 10 i 11 są opcjonalne, ale zalecane.
10. **Utworzenie pliku .gitignore (krok opcjonalny — best practice)**
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

11. **Weryfikacja instalacji Git i aktywacja (krok opcjonalny — best practice)**
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


12. **Dokumentacja Gemini API**
   Przed rozpoczęciem programowania warto zapoznać się z oficjalną dokumentacją Gemini API, która zawiera szczegółowe informacje o dostępnych funkcjach, parametrach i przykładach użycia:
   
   - Przewodnik szybkiego startu: https://ai.google.dev/gemini-api/docs/quickstart
   - Dokumentacja generowania tekstu: https://ai.google.dev/gemini-api/docs/text-generation
   
   > Dokumentacja API jest podstawowym źródłem wiedzy dla programisty. Zawiera szczegółowe informacje o wszystkich dostępnych funkcjach, ich parametrach, wartościach zwracanych oraz przykładach użycia. Regularne odwoływanie się do dokumentacji pomaga w efektywnym wykorzystaniu możliwości API i uniknięciu typowych błędów.

## Aplikacja terminalowa (app_terminal.py)

Aplikacje terminalowe są doskonałym punktem wyjścia do testowania API bez konieczności budowania złożonego interfejsu użytkownika. Pozwalają na szybkie eksperymenty i weryfikację działania API przed przejściem do bardziej złożonych implementacji. Poniższy kod stanowi najprostszy przykład komunikacji z API Gemini.

W najprostszej wersji, aplikacja terminalowa jest prostym skryptem bezpośrednio komunikującym się z API:

### 1. Bazowy chatbot
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
    contents="Wytłumacz działanie propagacji wstecznej w kontekście LLM - krótko"
)

# Wyświetlenie odpowiedzi
print(response.text)
```

Po zakończeniu implementacji aplikacji terminalowej, zapisz zmiany w repozytorium (krok opcjonalny — best practice):

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


## Rozbudowa aplikacji terminalowej — ćwiczenia (a–f)

W tej części będziesz inkrementalnie rozszerzać `app_terminal.py`. Każdy krok bazuje na poprzednim i jest zgodny z dokumentacją „Generowanie tekstu” Gemini API.

- 2) wprowadzenie parametru temperatury
- 3) wprowadzenie prompta systemowego
- 4) wprowadzenie reasoningu (zmień model na 2.5 Flash)
- 5) wprowadzenie streamingu
- 6) wprowadzenie chatu z historią
- 7) wprowadzenie multimodalności (czytanie pliku `image.png` z katalogu głównego)

Wskazówka: dalej używamy pakietu `google-genai`. Importujemy też `types` do konfiguracji.

### 2. Parametr temperatury (kontrola kreatywności)

Najprostsze ustawienie temperatury przez `GenerateContentConfig`.

```python
import os
from dotenv import load_dotenv
from google import genai
from google.genai import types

load_dotenv()
api_key = os.getenv("GEMINI_API_KEY")
client = genai.Client(api_key=api_key)

resp = client.models.generate_content(
   model="gemini-2.0-flash", 
   contents=["Wytłumacz działanie propagacji wstecznej w kontekście LLM - krótko"],
   config=types.GenerateContentConfig(temperature=0.2),
)
print(resp.text)
```

Krótko: niższa temperatura (np. 0.1) = bardziej zachowawcze; wyższa (np. 0.9) = bardziej kreatywne.

### 3. Prompt systemowy (sterowanie rolą modelu)

Użyj `system_instruction`, aby ustawić personę/zasady.

```python
import os
from dotenv import load_dotenv
from google import genai
from google.genai import types

load_dotenv()
api_key = os.getenv("GEMINI_API_KEY")
client = genai.Client(api_key=api_key)

resp = client.models.generate_content(
   model="gemini-2.0-flash",
   contents=["Napisz 3 wskazówki produktywności"],
   config=types.GenerateContentConfig(
      system_instruction="Jesteś asystentem-ekspertem. Pisz zwięźle po polsku."
   ),
)
print(resp.text)
```
Zaleca się umieszczenie promptów w dedykowanych zmiennych, co poprawia czytelność kodu i ułatwia jego dalszą rozbudowę oraz utrzymanie.

```python
import os
from dotenv import load_dotenv
from google import genai
from google.genai import types

load_dotenv()
api_key = os.getenv("GEMINI_API_KEY")
client = genai.Client(api_key=api_key)

user_prompt = "Napisz 3 wskazówki produktywności"
system_prompt = "Jesteś asystentem-ekspertem. Pisz zwięźle po polsku."

resp = client.models.generate_content(
   model="gemini-2.0-flash",
   contents=[user_prompt],
   config=types.GenerateContentConfig(
      system_instruction=system_prompt
   ),
)
print(resp.text)
```


Krótko: `system_instruction` ustawia „personę”/reguły modelu (np. ton, rola, format).

### 4. Reasoning: przejście na Gemini 2.5 Flash i budżet myślenia

Przełącz model na `gemini-2.5-flash`. „Myślenie” (reasoning) jest domyślnie włączone. Opcjonalnie możesz ustawić budżet myślenia.

```python
import os
from dotenv import load_dotenv
from google.genai import types
from google import genai

load_dotenv()
api_key = os.getenv("GEMINI_API_KEY")
client = genai.Client(api_key=api_key)

resp = client.models.generate_content(
   model="gemini-2.5-flash",
   contents=["Wyjaśnij paradoks kłamcy prostym językiem"],
   # config=types.GenerateContentConfig(
   #     thinking_config=types.ThinkingConfig(thinking_budget=0)  # 0 = wyłącza myślenie
   # ),
)
print(resp.text)
```

Krótko: 2.5 Flash poprawia reasoning; budżet 0 wyłącza myślenie (szybciej/taniej).

### 5. Streaming odpowiedzi (płynne wyświetlanie)

Użyj `generate_content_stream`, aby wypisywać fragmenty na bieżąco.

```python
import os
from dotenv import load_dotenv
from google import genai

load_dotenv()
api_key = os.getenv("GEMINI_API_KEY")
client = genai.Client(api_key=api_key)

response = client.models.generate_content_stream(
   model="gemini-2.0-flash",
   contents=["Opisz historię księstwa warszawskiego"],
)
for chunk in response:
   print(chunk.text, end="")
```

Krótko: streaming poprawia UX w terminalu — treść pojawia się przyrostowo.

### 6. Chat z historią (wieloturówka)

Przykład: kilka tur i podgląd historii.

```python
import os
from dotenv import load_dotenv
from google import genai

load_dotenv()
api_key = os.getenv("GEMINI_API_KEY")
client = genai.Client(api_key=api_key)
chat = client.chats.create(model="gemini-2.5-flash")

r1 = chat.send_message("Mam w domu 2 psy.")
print(r1.text)

r2 = chat.send_message("Ile łap jest w moim domu?")
print(r2.text)

for m in chat.get_history():
   print(f"{m.role}: {m.parts[0].text}")
```

Krótko: SDK utrzymuje historię; przy każdej turze wysyłana jest pełna konwersacja.

### 7. Multimodalność: tekst + obraz (`image.png` w katalogu głównym)

Wczytaj obraz i wyślij go razem z promptem. Wymaga biblioteki Pillow.

Upewnij się, że w katalogu projektu istnieje plik `image.png`. Jeśli nie masz Pillow, doinstaluj:
Dodaj linijkę Pillow w pliku requirements.txt oraz zainstaluj komendą 

```bash
pip install -r requirements.txt
```

Przykład:

```python
import os
from dotenv import load_dotenv
from PIL import Image
from google import genai

load_dotenv()
api_key = os.getenv("GEMINI_API_KEY")
client = genai.Client(api_key=api_key)
image = Image.open("image.png")

resp = client.models.generate_content(
   model="gemini-2.5-flash",
   contents=[image, "Co znajduje się na tym obrazie?"],
)
print(resp.text)
```

Krótko: API 2.5 przyjmuje obrazy jako wejście; możesz pytać np. „Co jest na obrazku?” lub „Podsumuj zawartość wykresu”.

### Notatki końcowe

- Parametry możesz łączyć: np. `--system`, `--temperature`, `--stream` działają jednocześnie.
- Dla multimodalności dodaj `Pillow` do `requirements.txt` lub instaluj ad-hoc.
- Dokumentacja, na której bazują powyższe przykłady: [Gemini - Generowanie tekstu](https://ai.google.dev/gemini-api/docs/text-generation)” (sekcje: system instruction, temperature, streaming, chat, multimodal).


## Co dalej?

Następnym krokiem będzie utworzenie modułu API Gemini, który posłuży jako wspólna warstwa komunikacji z API dla wszystkich naszych aplikacji. Przejdź do [modułu API](gemini-api-module.md), aby poznać szczegóły implementacji funkcji obsługującej Gemini API.

Po opanowaniu podstaw komunikacji z API, zajmiemy się budowaniem aplikacji desktopowych, które zapewnią bardziej przyjazny interfejs użytkownika do interakcji z modelem Gemini. Przejdź do [aplikacji desktopowych](aplikacje-desktopowe.md), aby dowiedzieć się, jak tworzyć aplikacje GUI przy użyciu Tkinter i PySide6.
