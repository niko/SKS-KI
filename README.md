tldr;

Die folgenden Files lassen sich direkt als Prompt für das LLM der Wahl kopieren:

* [Prompt.Fragenkatalog-SKS.Navigation.txt](https://raw.githubusercontent.com/niko/SKS-KI/refs/heads/main/Prompt.Fragenkatalog-SKS.Navigation.txt)
* [Prompt.Fragenkatalog-SKS.Schifffahrtsrecht.txt](https://raw.githubusercontent.com/niko/SKS-KI/refs/heads/main/Prompt.Fragenkatalog-SKS.Schifffahrtsrecht.txt)
* [Prompt.Fragenkatalog-SKS.Seemannschaft1.txt](https://raw.githubusercontent.com/niko/SKS-KI/refs/heads/main/Prompt.Fragenkatalog-SKS.Seemannschaft1.txt)
* [Prompt.Fragenkatalog-SKS.Seemannschaft2.txt](https://raw.githubusercontent.com/niko/SKS-KI/refs/heads/main/Prompt.Fragenkatalog-SKS.Seemannschaft2.txt)
* [Prompt.Fragenkatalog-SKS.Wetterkunde.txt](https://raw.githubusercontent.com/niko/SKS-KI/refs/heads/main/Prompt.Fragenkatalog-SKS.Wetterkunde.txt)

Probiert hab ich's recht ausführlich mit Nano Banana im Google AI Studio. Ein kurzer Test mit Google Gemini war auch ok.

TODO: Bilder fehlen noch


Extrahieren der Wissensgebiete aus dem PDF:
```
pdftotext -f 4 -l 27 Fragenkatalog-SKS.pdf Fragenkatalog-SKS.Nav1.txt
pdftotext -f 28 -l 53 Fragenkatalog-SKS.pdf Fragenkatalog-SKS.Schifffahrtsrecht.txt
pdftotext -f 54 -l 74 Fragenkatalog-SKS.pdf Fragenkatalog-SKS.Wetterkunde.txt
pdftotext -f 75 -l 112 Fragenkatalog-SKS.pdf Fragenkatalog-SKS.Seemannschaft1.txt
pdftotext -f 113 -l 147 Fragenkatalog-SKS.pdf Fragenkatalog-SKS.Seemannschaft2.txt
```

Vorformatieren der Txt-Files:
```
ls Fragenkatalog-SKS.*.txt | while read F; do
  sed -E 's/Nummer /Frage /g' -i $F
  sed -E 's/(Frage ([0-9]+):)/\1\n\Antwort \2:/g' -i $F
done
```
und dann eine Menge Handarbeit.

Um die Prompts zu generieren:
```
ls Fragenkatalog-SKS.*.txt | while read F; do
  cat Prompt.txt > "Prompt.${F}"
  echo >> "Prompt.${F}"
  echo "Fragenkatalog: " >> "Prompt.${F}"
  cat $F >> "Prompt.${F}"
  echo -n '* Stelle mir die Fragen in dieser Reihenfolge: ' >> "Prompt.${F}"
  grep -E '^Frage ' Prompt.Fragenkatalog-SKS.Navigation.txt | sed -E 's/Frage ([0-9]+):/\1/' | shuf | tr '\n' ',' >> "Prompt.${F}"
done
```

