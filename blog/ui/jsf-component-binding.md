

GET-Request:
1. getActionButton() → returns null
2. setActionButton(HtmlCommandButton) → neue Instanz + Listener
3. getActionButton() → gibt gebundene Komponente zurück

POST-Request:
1. setActionButton(HtmlCommandButton) → wiederhergestellte Instanz (kein neuer Listener!)
2. getActionButton() → gibt Komponente zurück
3. setActionButton(HtmlCommandButton) → ggf. erneute Bindung (aber Listener bereits vorhanden)
