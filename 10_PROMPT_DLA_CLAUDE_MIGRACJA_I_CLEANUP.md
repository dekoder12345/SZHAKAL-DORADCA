# PROMPT DLA CLAUDE — Migracja, cleanup, memory, docs, GitHub sync

Wklej to Claude'owi 1:1:

```text
Masz zrobić zamknięty sprint porządkowo-migracyjny dla workspace `SZHAKAL` przed przenosinami na nowy laptop.

Cel:
1. doprowadzić repo wykonawcze do stanu możliwie czystego i zrozumiałego,
2. zaktualizować pamięć projektu i dokumentację tak, żeby nowa sesja na nowym komputerze nie startowała od chaosu,
3. wypchnąć to na GitHuba,
4. zweryfikować na GitHubie, że wszystko renderuje się i wygląda poprawnie.

Kontekst:
- najważniejsze repo wykonawcze to `Scenariusze/`
- sibling repos:
  - `DataStories/`
  - `CodeTutorials/`
  - `ViralShorts/`
- `Scenariusze` jest aktualnie live/dirty repo i wymaga najwięcej uwagi
- `DataStories`, `CodeTutorials`, `ViralShorts` są na ten moment znacznie spokojniejsze

Stan, który musisz przyjąć jako punkt startu:
- `Scenariusze` na `main` jest ahead względem `origin/main`
- są lokalne modyfikacje i dużo untracked/generated rzeczy
- szczególnie:
  - zmodyfikowany `broll-engine/src/videos/index.ts`
  - zmodyfikowany `context_ingest.py`
  - chronione lokalne pliki w `broll-engine/src/videos/pszczoly-iddychanie-pod-woda/`
  - dużo nowych auto-generated katalogów w `broll-engine/src/videos/*`
  - dużo wygenerowanych `reports/factcheck/*` i `reports/plain/*`

Bezwzględne zasady:
- NIE ruszaj bez potrzeby:
  - `broll-engine/src/videos/pszczoly-iddychanie-pod-woda/broll_config.json`
  - `broll-engine/src/videos/pszczoly-iddychanie-pod-woda/index.tsx`
- nie rób `git add -A`
- nie rób ślepego `git clean`
- nie traktuj wszystkich untrackedów jako śmieci
- najpierw klasyfikujesz, dopiero potem decydujesz: commit / ignore / archive / delete

Tryb pracy:
- działasz wykonawczo, nie konsultacyjnie
- planujesz krótko i od razu realizujesz
- używasz terminala, przeglądarki i GitHuba do realnej weryfikacji
- jeśli drobny problem da się naprawić od ręki bez ukrytego ryzyka, naprawiasz go od razu
- jeśli pojawia się decyzja o dużych skutkach, zatrzymujesz się i raportujesz konkretnie

Zadania do wykonania

FAZA 1 — Audyt końcowy repo wykonawczych
- przejrzyj:
  - `Scenariusze/`
  - `DataStories/`
  - `CodeTutorials/`
  - `ViralShorts/`
- sprawdź:
  - `git status --short --branch`
  - `git log --oneline -n 10`
  - `git remote -v`
  - czy local HEAD jest przed origin
  - czy są artefakty wygenerowane, które nie powinny siedzieć luzem
- wypisz jasno:
  - co jest source
  - co jest generated but valuable
  - co jest generated noise
  - co jest protected user state

FAZA 2 — `Scenariusze` cleanup z głową
- uporządkuj `Scenariusze` pod migrację:
  - zdecyduj co powinno być śledzone
  - co powinno być ignorowane
  - co powinno być zarchiwizowane
  - co można bezpiecznie usunąć
- jeśli trzeba, popraw `.gitignore`
- jeśli `reports/` albo auto-generated `videos/*` mają być utrzymane jako trwały artefakt roboczy, opisz to w docs/memory
- jeśli nie, oczyść je lub wyłącz z repo zgodnie z sensem projektu

FAZA 3 — Aktualizacja pamięci i dokumentacji
- zaktualizuj dokumenty, żeby odzwierciedlały prawdę o obecnej architekturze
- minimum do sprawdzenia/aktualizacji:
  - root `PROJECT_MEMORY.md`
  - root `README.md`
  - `Scenariusze/CLAUDE.md`
  - jeśli trzeba: `Scenariusze/README.md`
- dokumentacja ma odzwierciedlać aktualny realny pipeline `Scenariusze`, w tym:
  - ETAP -1
  - ETAP 0
  - Faza 1
  - Faza 1.5
  - Faza 1.7
  - Faza 2
  - Faza 4
  - Faza 5
  - Faza 6
  - `--category`
  - metadata
  - verify/simplify
  - ingest kontekstu
  - hooki
  - pakiet dla montażysty

FAZA 4 — Spójność cross-repo
- sprawdź, czy sibling repos nadal mają sensowny README / CLAUDE.md / metadata flow
- jeśli drobna korekta docs jest potrzebna, zrób ją
- bez wielkich refaktorów

FAZA 5 — Verify lokalny
- uruchom sensowne smoke testy:
  - `python pipeline_v3.py --help`
  - `python pipeline_v3.py --category CIEKAWOSTKI --only-context` lub równoważny tani test
  - bezpieczne `--help` / metadata help / key CLI checks w sibling repos
- jeśli zmieniasz TSX lub rejestry, odpal też odpowiedni `tsc --noEmit`
- udowodnij, że po cleanupie nic ważnego się nie posypało

FAZA 6 — Commit + push
- staging selektywny
- logiczne commity
- push do odpowiednich repo
- żadnego `--no-verify` bez twardego powodu

FAZA 7 — Weryfikacja na GitHubie
- sprawdź przez GitHub / web UI:
  - ostatnie commity są widoczne
  - README renderuje się poprawnie
  - ważne pliki pamięci/docs są obecne
  - repo wygląda sensownie dla nowego komputera / nowego startu

Definicja sukcesu
- `Scenariusze` nie jest chaotycznym worktree’em pełnym nieopisanych artefaktów
- pamięć projektu jest aktualna
- dokumentacja odzwierciedla realny stan
- repo są wypchnięte
- GitHub wygląda dobrze
- nowy komputer będzie mógł po prostu zrobić clone i ruszyć dalej

Raport końcowy:
- co zastałeś
- co sklasyfikowałeś jako source / generated / protected
- co poprawiłeś
- jakie pliki docs/memory zaktualizowałeś
- jakie commity i pushe poszły
- co sprawdziłeś lokalnie
- co sprawdziłeś na GitHubie
- czy workspace jest gotowy do migracji na nowy laptop
```

Uwagi dla mnie:

- ten prompt zakłada, że Claude ma dostęp do lokalnego workspace i GitHub auth po swojej stronie
- przed uruchomieniem warto mu jeszcze wkleić aktualny `git status` z `Scenariusze`, jeśli przez
  chwilę zdąży się zmienić
