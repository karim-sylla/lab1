# Aplikacja Streamlit

Streamlit to nowoczesna biblioteka Pythona zaprojektowana specjalnie do tworzenia aplikacji webowych dla projektów związanych z danymi i sztuczną inteligencją. Wyróżnia się wyjątkowo prostym API, które pozwala przekształcić skrypty Pythona w interaktywne aplikacje webowe z minimalnym nakładem pracy. Dzięki automatycznemu odświeżaniu i wbudowanym widżetom, Streamlit jest idealnym wyborem do szybkiego prototypowania i prezentacji możliwości modeli AI.

## Przygotowanie środowiska do aplikacji Streamlit

1. **Aktualizacja pliku requirements.txt**
   Możesz dodać  wartość "streamlit" do pliku `requirements.txt` ręcznie lub wykonać poniższą komendę w terminalu (bash):
   ```bash
   echo "streamlit" >> requirements.txt
   pip install -r requirements.txt
   ```

## Kod aplikacji (streamlit_app.py)

Możesz wykorzystać GitHub Copilot do wygenerowania kodu aplikacji Streamlit:

```
Zbuduj aplikację Streamlit do interakcji z Gemini API z elementami:
- suwak temperatury (0.0-1.0)
- suwak maksymalnej liczby tokenów (100-2048)
- pole tekstowe na instrukcję systemową
- pole tekstowe na zapytanie użytkownika
- przycisk generowania
- wyświetlanie odpowiedzi
Wykorzystaj funkcję generate_response() z gemini_api.py
```

Przykładowy kod:

```python
import streamlit as st
from gemini_api import generate_response

# Konfiguracja strony
st.set_page_config(page_title="Gemini API - Aplikacja Streamlit", layout="wide")
st.title("Gemini API Demo")

# Suwak temperatury
temperature = st.slider("Temperatura", min_value=0.0, max_value=1.0, value=0.7, step=0.1)

# Suwak liczby tokenów
max_tokens = st.slider("Maksymalna liczba tokenów", min_value=100, max_value=2048, value=1024, step=50)

# Pole na wpisanie tekstu systemowego
system_prompt = st.text_area("Tekst systemowy", value="Jesteś pomocnym asystentem AI.", height=100)

# Pole na wpisanie zapytania
user_prompt = st.text_area("Wpisz swoje zapytanie", height=150)

# Przycisk zatwierdzenia
if st.button("Generuj odpowiedź"):
    if user_prompt:
        with st.spinner("Generowanie odpowiedzi..."):
            response_text = generate_response(
                prompt=user_prompt,
                system_prompt=system_prompt,
                temperature=temperature,
                max_tokens=max_tokens
            )
            
            # Wyświetlenie wyniku
            st.subheader("Odpowiedź:")
            st.write(response_text)
    else:
        st.warning("Proszę wprowadzić zapytanie.")
```

Po zaimplementowaniu aplikacji Streamlit, zapisz zmiany w repozytorium:

```bash
git add streamlit_app.py
git commit -m "Implementacja aplikacji webowej Streamlit"
```

## Uruchamianie aplikacji

Aby uruchomić aplikację Streamlit:

```bash
streamlit run streamlit_app.py
```

Aplikacja będzie dostępna w przeglądarce pod adresem http://localhost:8501.

## Zalety Streamlit

1. **Szybkie prototypowanie** - tworzenie interfejsu webowego w kilku liniach kodu
2. **Interaktywne elementy** - wbudowane widżety (suwaki, przyciski, pola tekstowe)
3. **Automatyczne odświeżanie** - aplikacja odświeża się po zmianie kodu
4. **Łatwe wdrażanie** - proste udostępnianie aplikacji
5. **Integracja z ekosystemem data science** - współpraca z bibliotekami do analizy danych

## Struktura projektu

```
gemini_projekt/
├── gemini_api.py        # Wspólny moduł API
├── app_terminal.py      # Aplikacja terminalowa
├── tkinter_app.py       # Aplikacja Tkinter
├── pyside_app.py        # Aplikacja PySide6
├── streamlit_app.py     # Aplikacja Streamlit
├── .env                 # Plik z kluczem API
└── requirements.txt     # Wymagane zależności
```

Streamlit jest idealnym narzędziem do szybkiego prototypowania aplikacji AI, tworzenia dashboardów i demonstracji możliwości modeli językowych.

## Co dalej?

Po zapoznaniu się z tworzeniem aplikacji w Streamlit, czas na poznanie bardziej zaawansowanego frameworka webowego - Flask. Przejdź do [ostatniej części](aplikacja-flask.md), aby nauczyć się budować profesjonalne aplikacje internetowe z pełną kontrolą nad architekturą i interfejsem.