# Projekt ETL + Power BI: Spotify Streaming History

## Cel projektu
Celem projektu jest demonstracja procesu ETL (Initial+Delta Load) z wykorzystaniem **Pentaho Data Integration (Spoon)** oraz budowy interaktywnego raportu w **Power BI**.

Projekt stanowi przykład integracji procesów ładowania danych z wizualizacją w celu zaprezentowania umiejętności analitycznych i technicznych.

---

## Technologie
- **ETL**: Pentaho Spoon (PDI)
- **Źródło danych**: plik CSV
- **Baza danych docelowa**: MSSQL
- **Raportowanie**: Power BI Desktop

---

## ETL: Initial+Delta Load

### Opis działania:
- Odczyt danych z pliku CSV 
- Transformacja danych (m.in. czyszczenie, typowanie, joiny)
- Załadunek do bazy danych (tabela faktów i wymiary)

### Pliki:
- 'etl_initial/get data from source to staging.kjb` – transformacja z pełnym pierwszym załadowaniem do warstwy staging
- 'etl_initial/FindLastLoad-initial load.ktr` – transformacja, która za pomocą Set variables wyszukuje nam ostatnią pobraną wartość (tj.maximum) w kolumnie daty
  tabeli znajdującej się w ostatecznej, podstawowej warstwie (core layer). W tym momencie na potrzeby utworzenia core layer, ustawiłam MAX = LastLoad, a nie większe.
- 'etl_initial/get last load date - initial load.ktr` - transformacja do zaladowania danych z staging do core layer z przygotowaniem pod delta load
- 'etl_initial/uniquerows.kjb` - transformacja usuwania duplikatów
- 'etl_initial/load and transform.ktr`– transformacja z oczyszczaniem danych 
- 'etl_initial/job 1 - initial_load.kjb` - job z transformacją danych
- 'etl_initial/job 2 - core dimension tables.kjb` - job z tworzeniem tabeli faktów i wymiarów w core layer


### Podgląd joba:
![screenshots](Job 1 - initial load.png)
![screenshots](Job 2 - core dimensions tables.png)
![screenshots](transform and load to staging.png)
![screenshots](initial_load_SQL.png)

---

## ETL – Delta Load

### Opis:
- Identyfikacja i załadunek tylko nowych rekordów
- Porównanie danych wejściowych z istniejącymi w bazie (na podstawie daty)
- Aktualizacja danych w bazie (insert/update)

### Mechanizm:
- Wczytanie nowych danych z pliku CSV (symulacja aktualizacji)
- Join z danymi docelowymi po kluczu głównym
- Użycie **Step: Merge Rows (diff)**  w Spoon
- Warunkowe **Insert** / **Update**

### Pliki:
- 'etl_delta/FindLastLoad-deltaload.kjb`
- 'etl_delta/get last load date-deltaload.ktr` - transformacja do zaladowania danych z staging do core layer (Max > LastLoad)
- 'etl_delta/uniquerows.kjb` - transformacja usuwania duplikatów
- 'etl_delta/ Job 3 - deltaload.kjb` - job z transformacjami do wykonania kolejnych załadowań danych
- 'etl_delta/job 2 - core dimension tables.kjb` - job z tworzeniem tabeli faktów i wymiarów w core layer
- 'etl_delta/ CompleteETLprocess.kjb` - job nadrzędny zawierający połączenie Job 3 i Job 2

### Zrzut joba Delta:
![screenshots](Job 3 - delta load.png)
![screenshots](Job 2 - core dimension tables.png)
![screenshots](completeETLproces - deltaload.png)
![screenshots](delta_load_SQL.png)

---

## Power BI – Raport sprzedaży

Raport został zbudowany na danych zaczytanych przez proces ETL.

### Zakres wizualizacji:
- KPI
- Top Artyści, Piosenki, Albumy
- Ilość odtworzeń
- Długość czasu odtworzeń
- Dni z najczęstszym odsłuchiwaniem


### Plik:
- `powerbi_report/Spotify History.pbix`

### Podgląd raportu:
https://app.powerbi.com/view?r=eyJrIjoiM2I0YWY0YjctNTliMS00NzA2LTgxMzktYTYxNDgwODIxNDFlIiwidCI6ImNlMDkwY2ZlLTkzZGQtNDE1Yy04NDM3LTljZTI3ODNiNGI2MSJ9

### Aktualizacja danych po Delta Load:
Po wykonaniu joba delta, Power BI automatycznie pobiera zaktualizowane dane (kolejne rekordy w tabeli faktów i wymiarów). Dzięki temu użytkownik widzi realne zmiany wynikające z działania procesu.

### Plik:
- `powerbi_report/Spotify_History.pbix`

### Zrzut ekranu:
![screenshots](initial_load_powerbi.png)
![screenshots](delta_load_powerbi.png)
---

### Video:
![video](DeltaLoad&RefreshPowerBi.mp4)

## Możliwe rozszerzenia:
-  Delta Load: ładowanie kolejnych nowych danych
-  Harmonogram: uruchamianie joba cyklicznie
-  Dodanie kolejnych źródeł danych (np. JSON)
-  Obsługa błędów i walidacja danych

---

## 👤 Autor
**Nina Lange**  
 ninamalgolange@gmail.com
 [LinkedIn](https://www.linkedin.com/in/ninalange-/)  
 [Portfolio GitHub](https://github.com/NinaMalgo)

---

##  Status projektu
 Initial Load + Raport Power BI – **Zakończono**  
 Delta Load – **wdrożony**  
