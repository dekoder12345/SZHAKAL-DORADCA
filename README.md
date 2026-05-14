# Doradcy/SZHAKAL — Codex advisor setup

Folder zawiera pakiet kontekstowy dla przyszlych sesji Codexa. To nie jest repo kodu,
tylko zestaw plikow, ktore maja przenosic role, zasady, historie decyzji i najnowsza
pamiec projektu po zamknieciu sesji.

## Co jest w tym folderze

| Plik | Rola | Kiedy czytac |
|---|---|---|
| `00_ROLE.md` | Definicja roli Codexa | Zawsze pierwszy |
| `01_PROJECT_CONTEXT.md` | Pelny kontekst projektu i aktualny stan | Drugi |
| `02_ADVISORY_RULES.md` | Jak Codex ma odpowiadac i reviewowac | Trzeci |
| `03_DECISION_FRAMEWORK.md` | Typowe scenariusze i rekomendacje | Czwarty |
| `04_ANTI_PATTERNS.md` | Historia porazek i rzeczy, ktorych nie powtarzac | Piąty |
| `05_WORKING_MEMORY.md` | Najnowsza pamiec sprintow, decyzji i stanu | Szosty, przy kazdej nowej sesji |
| `06_SESSION_LOG_2026-04-20.md` | Skondensowany log duzej sesji stabilizacyjnej | Opcjonalnie, gdy liczy sie "dlaczego" |
| `07_SESSION_LOG_2026-04-21.md` | Skondensowany log kolejnej fali zmian (metadata, ETAP 0, docs drift) | Opcjonalnie, gdy liczy sie "dlaczego" |
| `08_SESSION_LOG_2026-05-13.md` | Snapshot stanu pod migracje na nowy laptop | Opcjonalnie, gdy liczy sie "dlaczego" |
| `11_SESSION_LOG_2026-05-14.md` | Potwierdzenie publikacji repo doradczego na GitHubie | Opcjonalnie, gdy liczy sie "dlaczego" |
| `09_NEW_LAPTOP_BOOTSTRAP.md` | Konkretna instrukcja odtworzenia setupu na nowej maszynie | Dla Ciebie i dla AI |
| `10_PROMPT_DLA_CLAUDE_MIGRACJA_I_CLEANUP.md` | Gotowy super-prompt dla Claude do sprzatania repo wykonawczych | Do przeklejenia 1:1 |
| `README.md` | Ten plik | Dla Ciebie, nie dla AI |

## Jak tego uzywac z Codexem

### Opcja A: Codex CLI

```bash
codex --context-from "C:\Users\Nikiodem\Desktop\Codex Projekty\Doradcy\SZHAKAL"
```

### Opcja B: ChatGPT Projects / Custom GPT

Uploaduj co najmniej:

- `00_ROLE.md`
- `01_PROJECT_CONTEXT.md`
- `02_ADVISORY_RULES.md`
- `03_DECISION_FRAMEWORK.md`
- `04_ANTI_PATTERNS.md`
- `05_WORKING_MEMORY.md`

Opcjonalnie dorzuc:

- `06_SESSION_LOG_2026-04-20.md`
- `07_SESSION_LOG_2026-04-21.md`
- `08_SESSION_LOG_2026-05-13.md`
- `11_SESSION_LOG_2026-05-14.md`

### Opcja C: Drugie Claude jako sparing partner

Jesli chcesz miec osobne Claude do doradztwa, wklej mu pliki `00`-`05` jako pierwszy
duzy context dump. `06` jest przydatny tylko gdy chcesz odtworzyc motywacje decyzji.

## Jedna komenda startowa

Po zaladowaniu plikow wklej:

> Przeczytaj `00_ROLE.md`, `01_PROJECT_CONTEXT.md`, `02_ADVISORY_RULES.md`,
> `03_DECISION_FRAMEWORK.md`, `04_ANTI_PATTERNS.md` i `05_WORKING_MEMORY.md`
> w tej kolejnosci. Jesli task dotyczy ostatniej fali sprintow, doczytaj tez
> `06_SESSION_LOG_2026-04-20.md`. Nie podsumowuj. Od tej pory jestes Doradca
> projektu SZHAKAL i bedziesz oceniac propozycje Claude Code. Bede pisal po polsku.

## Kiedy aktualizowac pamiec

Aktualizuj ten pakiet, gdy wydarzy sie cos istotnego:

- nowy pipeline
- duzy revert
- zmiana stacku lub workflow
- nowa twarda regula
- duza sesja sprintowa, po ktorej warto zachowac decyzje i stan

Praktyczna zasada:

1. `01_PROJECT_CONTEXT.md` — aktualny stan projektu
2. `05_WORKING_MEMORY.md` — najnowsza pamiec operacyjna
3. `06_SESSION_LOG_YYYY-MM-DD.md` — tylko jesli warto zachowac przebieg decyzji

## Po co to istnieje

Ten folder ma sprawic, ze nowa sesja nie zaczyna od zera. Codex ma od razu wiedziec:

- co juz zostalo zrobione
- co jest stabilne
- czego nie ruszac bez triggera
- jakie byly decyzje Codex vs Claude
- jakie lokalne zmiany uzytkownika trzeba chronic
