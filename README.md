### Faza 1: Budowa rozszerzonego grafu wiedzy

**Cel:** Zgromadzenie i integracja danych z wielu źródeł, stworzenie bogatego grafu.

**Zadania:**

- Setup Neo4j na Docker
- Inicjalne zasilenie danymi z **IMDb30** (gotowe triplety)
- Rozszerzenie grafu o dane z **MovieGraphBenchmark** (relacje z TMDB)
- Import danych z **FIDA** – dodanie kontekstu europejskiego (festiwale, dystrybucja)
- Normalizacja i deduplikacja encji (aktorzy, tytuły)
- Ekstrakcja cech semantycznych z opisów filmów za pomocą embeddingów (Ollama) – zapis w wektorowej bazie (Chroma/FAISS)

### Faza 2: Wieloagentowy silnik rekomendacji

**Cel:** Implementacja inteligentnego systemu z wieloma wyspecjalizowanymi agentami.

**Zadania:**

- Implementacja warstwy LangChain tłumaczącej język naturalny na zapytania Cypher
- **Budowa agentów** (LangGraph):
  - **Agent Grafowy** – odpowiada za zapytania strukturalne (reżyserzy, aktorzy, lata)
  - **Agent Nastroju** – analizuje embeddingi opisów, znajduje filmy o podobnym klimacie
  - **Agent Stylu Wizualnego** – rozpoznaje estetykę na podstawie słów kluczowych (np. "neonowy noir", "cinematic long takes") 
  - **Agent Personalizacji** – zarządza profilem użytkownika i historią (zapis w JSON)
  - **Supervisor Agent** – koordynuje pracę pozostałych agentów (LangGraph)
- Konfiguracja lokalnego LLM (Ollama) do interpretacji wyników i generowania odpowiedzi
- Implementacja **streamingu odpowiedzi** w terminalu (token po tokenie)

### Faza 3: Terminalowy interfejs użytkownika

**Cel:** Stworzenie angażującego doświadczenia w terminalu z elementami interaktywnymi.

**Zadania:**

- Budowa interfejsu w Pythonie z użyciem bibliotek:
  - `rich` – kolorowe formatowanie, panele, progres bary
  - `prompt_toolkit` – autouzupełnianie, historia komend
  - `ascii_magic` – wyświetlanie ASCII art z okładek filmów (opcjonalnie)
- **Główne tryby pracy**:
  1. **Tryb czatu** – swobodna rozmowa z asystentem (streaming)
  2. **Tryb eksploracji grafu** – interaktywne menu do przeglądania relacji:
     - Wyświetlanie węzłów i połączeń w formacie drzewa
     - Możliwość wyboru węzła i "wejścia głębiej"
  3. **Tryb personalizacji** – przeglądanie i edycja profilu
- **Wizualizacja procesu**:
  - Logi agentów na bieżąco (kto co robi)
  - Podświetlanie ścieżki w grafie (ASCII representation)
  - Progress bar podczas długich operacji

### Faza 4: Testy i bezpieczeństwo

**Cel:** Ewaluacja, walidacja i zabezpieczenie systemu.

**Zadania:**

- **Testy jednostkowe** (pytest):
  - Poprawność generowania zapytań Cypher
  - Działanie poszczególnych agentów
- **Testy integracyjne**:
  - Spójność danych między źródłami
  - Wydajność zapytań grafowych (<2s)
- **Testy bezpieczeństwa**:
  - Walidacja wejść użytkownika (ochrona przed prompt injection) – regex + whitelist
  - Parametryzacja zapytań Cypher (ochrona przed injection)
  - Uprawnienia tylko do odczytu dla agenta grafowego
  - Filtry na wyjściu modelu (blokowanie nieodpowiednich treści)
  - **Tryb "safe"** – opcja uruchomienia z ograniczonym dostępem do API
- **Metryki**:
  - *Faithfulness* – czy odpowiedź bazuje na faktach z grafu (automatyczna weryfikacja)
  - *Relevance* – czy odpowiedź jest na temat (embedding podobieństwo)
  - *Novelty* – czy rekomendacje wprowadzają użytkownika w nowe obszary
