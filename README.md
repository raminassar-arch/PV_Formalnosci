# PV Formalność — wersja 1.3.0 (Python)

Natywna aplikacja desktopowa w **Python 3.13 + customtkinter 6.0**. To nowa,
główna wersja programu. Dotychczasowy plik
`..\PV_Formalność_v1.2.0.exe` (C# / .NET) pozostaje niezmieniony jako wersja
zapasowa.

## Najważniejsze cechy

- Kalkulator ścieżki formalnej dla instalacji fotowoltaicznej — logika
  przeniesiona 1:1 z `pv-formalnosc-native/Model.cs`.
- Licencja offline: w exe zapisane są **wyłącznie hashe SHA-256** kluczy
  (1000 dożywotnich + 1000 rocznych). Program nie łączy się z internetem.
- 14-dniowy okres próbny od pierwszego uruchomienia.
- Klucz roczny ważny **365 dni od pierwszej aktywacji**.
- Stan licencji współdzielony z wersją C#:
  - `%LOCALAPPDATA%\PV_Formalność\license.json`
  - `HKCU\Software\RamiNassar\PVFormalnosc` (wartość `FirstStartUtc`)
- Bezpośredni eksport **PDF** (bez drukarki i bez przeglądarki) oraz **XLSX**.
- Nowoczesny, jasny motyw z responsywnym układem kart i zawijaniem opisów.

## Uruchamianie ze źródeł

Wymagania: Python 3.13 oraz pakiety `customtkinter`, `pillow`.

```bat
run.bat
```

albo:

```bat
python -m pv_formalnosc.app
```

## Testy

Pełny zestaw 17 testów (licencje, progi, ścieżki, PDF, XLSX, ważność kluczy):

```bat
python -m pv_formalnosc.app --selftest selftest.txt .
```

Wynik trafia do `selftest.txt`, a przykładowe pliki do
`selftest_sample.pdf` i `selftest_sample.xlsx`.

Gotowy exe przyjmuje te same argumenty. Ponieważ ścieżki zawierają spacje,
w PowerShell trzeba je zacytować:

```powershell
$exe = '..\PV_Formalność_v1.3.0.exe'
$out = "$env:TEMP\pv_selftest.txt"
$root = (Get-Location).Path
Start-Process $exe -ArgumentList @('--selftest', "`"$out`"", "`"$root`"") -Wait
Get-Content $out
```

Argument `root` musi wskazywać katalog projektu, bo tam leżą listy kluczy
użyte w testach (`keys\`). Bez nich test nadal działa, ale pomija 6 kontroli
dotyczących kluczy.

## Budowa jednego pliku exe

```bat
build.bat
```

Wynik: `dist\PV_Formalnosc_v1.3.0.exe`.

Skrypt buduje `main.py`, a nie bezpośrednio `pv_formalnosc\app.py`. To ważne:
PyInstaller uruchamia wskazany plik jako skrypt, więc bez cienkiego wrappera
relative importy wewnątrz pakietu kończą się błędem
`attempted relative import with no known parent package`.

Dane dołączane do exe:

- `pv_formalnosc\data` — hashe SHA-256, fonty DejaVu Sans, ikona,
- `keys` — listy kluczy potrzebne tylko przez `--selftest`.

## Gotowe pliki

| Plik | Rola | Rozmiar | SHA-256 |
|---|---|---:|---|
| `..\PV_Formalność_v1.3.0.exe` | nowa wersja główna | 32 853 839 B | `5FF0CF566CC3C6CC5D66A9085D32BFBCDD3679E5826612E4DA8092FE2332F9F6` |
| `..\PV_Formalność_v1.2.0.exe` | fallback C#, bez zmian | 65 982 890 B | `DA1397A593AF11F29A7CFA636DD08810A1B5F1867DAD2FD4B638DF62BF3CED37` |

Wersja 1.3.0 przeszła 17/17 testów zarówno ze źródła, jak i z finalnego exe.
Sprawdzono też zrzuty ekranu okna głównego, okna licencji oraz stanu
po wygaśnięciu okresu próbnego.

## Struktura

```
pv-formalnosc-python\
├─ main.py                  # wrapper dla PyInstaller
├─ run.bat / build.bat
├─ keys\                    # listy kluczy (tylko testy)
└─ pv_formalnosc\
   ├─ app.py                # punkt wejścia + --selftest
   ├─ ui.py                 # customtkinter: okno główne i licencja
   ├─ license.py            # SHA-256, próbka, rejestr i license.json
   ├─ model.py              # kalkulator i opisy kroków
   ├─ pdf_export.py         # PDF 1.4 z rasteryzowanych stron
   ├─ xlsx_export.py        # XLSX przez zipfile + XML
   ├─ theme.py              # paleta i ikona
   ├─ version.py            # 1.3.0
   └─ data\                 # hashe, fonty, app.ico
```

## Uwaga prawna

Wynik kalkulatora ma charakter informacyjny i nie jest poradą prawną.
Źródła i datę obowiązywania przepisów podano w interfejsie, PDF oraz XLSX.
