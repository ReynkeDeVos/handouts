# Git-Befehle Übersicht (Basics & PR-Flow)

## Projekt initial starten

```bash
git init
git add .
git commit -m "first commit"
```

---

## Remote auf GitHub anlegen & verknüpfen

1. Repo auf GitHub erstellen (Owner + Name)

2. Lokales Repository mit dem online GitHub Repository verknüpfen:

```bash
git remote add origin git@github.com:yourGHhandle/example.git
git branch -M main
git push -u origin main
```

> **Hinweis:** `-u` setzt die Upstream-Verknüpfung, danach reicht `git push` innerhalb dieser Branch `main`.

3. **main Branch schützen** vor nicht-Pull Request Merge-Anfragen

   Auf GitHub: In den Repo-Settings → Branches → Branch protection rule für main/default erstellen (z. B. „Require pull request before merging").

---

## Feature entwickeln (Branch-Workflow)

```bash
git switch -c <branchname>
git add . && git commit -m "this is my change"  # Änderungen speichern
git push -u origin <branchname>     # einen Pull Request erstellen ODER einen PR ergänzen (erkennt GitHub/git automatisch, wenn es die gleiche Branch ist)
```

- Auf der GitHub-Seite Pull Request erstellen, falls es noch keinen gibt

---

## Merge-Konflikte lösen (lokal)

```bash
git switch main
git pull  # lokales main mit dem online GitHub Repository synchronisieren (shorthand für git fetch && git merge)
git switch <branchname>
git merge main  # aktualisiertes main in den Feature-Branch mergen & Konflikte auslösen
```

- Konflikte im Merge Editor von VSCode lösen ([Video-Tutorial ansehen](https://www.youtube.com/watch?v=HosPml1qkrg))

```bash
git add . && git commit -m "resolve merge conflicts"
git push origin <branchname>  # den PR aktualisieren mit den gelösten Konflikten
```

> so kann ohne Probleme online der branch in main oder staging gemerged werden

---

## Ups! Ich habe direkt auf main gearbeitet

```bash
git switch -c <branchname>
git push -u origin <branchname>
git switch main
git fetch origin  # holt sich die neusten Metadaten/Commits von GitHub
git reset --hard origin/main  # Setzt deinen lokalen Branch main exakt auf den Stand von origin/main
git switch <branchname>  # weiter im richtigen Branch arbeiten
```

---

## Praktische Shortcuts & Checks

```bash
git status  # aktueller Zustand
git log --oneline --graph  # kompakter Verlauf mit Branch-Graph
```

---

# 💡 Tipp für Fortgeschrittene: Merge-Konflikte auch mit `git rebase` lösen (statt `merge`)

Wenn ihr euren Branch auf den neuesten Stand von `main` bringen wollt, habt ihr zwei Möglichkeiten:

- `git merge`
- `git rebase`

## 🧩 Beispiel-Situation

Ihr habt z. B. einen Feature-Branch:

```
main:    A --- B --- C
          \
feature:    D --- E
```

Inzwischen wurden neue Commits (`B`, `C`) auf `main` hinzugefügt.  
Ihr wollt jetzt euren Branch aktualisieren.

---

## 💥 Variante 1: Merge

```bash
git switch main
git pull
git switch <branchname>
git merge main              # ggf. Konflikte lösen
git push
```

Das ergibt:

```
main:    A --- B --- C ------ PRM (GitHub PR Merge)
           \               \  /
feature:    D --- E ------- M
                            ↑
                      Merge-Commit (main→feature)
```

✅ **Ist wunderbar**  
❌ **Aber:** Die Historie wird verzweigt und potentiell etwas unübersichtlich.

---

## 🌿 Variante 2: Rebase

```bash
git switch <branchname>
git pull --rebase origin main     # shorthand für: git fetch origin && git rebase origin/main
```

Git „nimmt" eure Commits (`D`, `E`), setzt sie kurz beiseite, springt auf den neuesten Stand von `main`, und „spielt" eure Änderungen oben drauf:

```
main:    A --- B --- C ------- PRM (GitHub PR Merge)
                      \        /
feature:               D' --- E'
```

✅ **Lineare Historie**  
❌ **Commit-IDs ändern sich** → also nur machen, wenn ihr allein auf diesem Branch arbeitet!

---

## ⚙️ Wenn Konflikte entstehen

Während des Rebases stoppt Git bei jedem Konflikt:

```bash
# Konflikte in VS Code lösen
git add .
git rebase --continue
```

Falls ihr abbrechen wollt:

```bash
git rebase --abort
```

---

## 🚀 Danach pushen

Da Rebase die Commit-Historie neu schreibt, müsst ihr mit `--force-with-lease` pushen:

```bash
git push --force-with-lease       # nach einem Rebase (Historie wurde umgeschrieben)
git push                          # wenn nur neue Commits hinzugefügt (kein Rebase)
```

💡 **Sicherer als `--force`**, da Git prüft, ob jemand anderes zwischenzeitlich gepusht hat.

---

## 🧠 Wann `rebase`, wann `merge`?

| Situation                               | Empfehlung |
| --------------------------------------- | ---------- |
| Ihr arbeitet alleine auf eurem Branch   | `rebase`   |
| Ihr arbeitet im Team auf einer Branch   | `merge`    |
| Ihr wollt die PR-Historie linear halten | `rebase`   |
| Ihr wollt Konflikte sichtbar lösen      | `merge`    |

---

## 📘 Kurz gesagt:

- **`merge`** = einfach, sicher, aber mit extra Commit-Nachrichten
- **`rebase`** = elegant & linear, aber verändert Historie
