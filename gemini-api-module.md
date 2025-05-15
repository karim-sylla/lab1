# Moduł API Gemini (gemini_api.py)

Ten moduł zawiera funkcjonalność komunikacji z Google Gemini API, która jest współdzielona przez wszystkie interfejsy aplikacji.

```python
import os
from dotenv import load_dotenv
from google import genai
from google.genai import types

def init_client():
    """Inicjalizuje klienta Gemini API"""
    load_dotenv()  # Załadowanie zmiennych środowiskowych
    api_key = os.getenv("GEMINI_API_KEY")
    return genai.Client(api_key=api_key)

def generate_response(prompt, system_prompt="", temperature=0.7, max_tokens=1024):
    """
    Generuje odpowiedź z modelu Gemini
    
    Args:
        prompt (str): Zapytanie użytkownika
        system_prompt (str): Instrukcja systemowa (opcjonalna)
        temperature (float): Parametr temperature (0.0-1.0)
        max_tokens (int): Maksymalna liczba tokenów w odpowiedzi
        
    Returns:
        str: Odpowiedź z modelu
    """
    client = init_client()
    
    try:
        # Konfiguracja generowania treści z systemową instrukcją
        config = types.GenerateContentConfig(
            temperature=temperature,
            max_output_tokens=max_tokens,
            system_instruction=system_prompt
        )
        
        # Generowanie odpowiedzi
        response = client.models.generate_content(
            model="gemini-2.0-flash",
            contents=prompt,
            config=config
        )
        
        return response.text
    except Exception as e:
        return f"Błąd: {str(e)}"
```

## Wyjaśnienie kodu

Moduł zawiera dwie główne funkcje:

1. `init_client()` - inicjalizuje klienta Gemini API, wczytując klucz API z pliku `.env`
2. `generate_response()` - główna funkcja generująca odpowiedź z modelu Gemini, przyjmująca parametry:
   - `prompt` - zapytanie użytkownika
   - `system_prompt` - instrukcja systemowa (opcjonalna)
   - `temperature` - parametr losowości odpowiedzi (0.0-1.0)
   - `max_tokens` - maksymalna długość odpowiedzi

Kod obsługuje dwa rodzaje zapytań:
- Z instrukcją systemową (`system_instruction`)
- Bez instrukcji systemowej (standardowe zapytanie)

Moduł wykorzystuje bibliotekę `dotenv` do wczytywania zmiennych środowiskowych i obsługuje wyjątki, zwracając komunikat błędu w przypadku problemów z generowaniem odpowiedzi.