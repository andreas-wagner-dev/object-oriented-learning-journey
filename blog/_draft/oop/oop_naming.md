# Namen sind kein Stilmittel, sie sind das Design.

Paket-, Klassen- und Methodennamen bilden gemeinsam die lesbare Oberfläche eines Systems. 
Die Paketstruktur definiert die Statik auf der Makroebene, Klassennamen bestimmen die Klarheit auf der Mikroebene, Methodennamen die Präzision auf der Interaktionsebene.  
Schlechte Namen auf jeder dieser Ebenen sind nicht nur ästhetisch störend – sie verraten ein grundlegend falsches Design.

# Namen sind kein Stilmittel – sie sind das Design.

Paket-, Klassen- und Methodennamen bilden die lesbare Oberfläche eines Systems. Während die Paketstruktur die Architektur auf der Makroebene definiert, bestimmen Klassennamen die Klarheit auf der Mikroebene und Methodennamen die Präzision der Interaktion. Schlechte Namen auf diesen Ebenen sind mehr als nur ästhetisch störend: Sie sind das Symptom eines grundlegend falschen Designs.

## Die Ursache: Das Diktat der Vorbilder

Schlechte Namen entstehen selten aus Nachlässigkeit, sondern aus der Macht der Gewohnheit. 

Die Vorbilder sind allgegenwärtig: Unzählige Fachbücher, Blogs und Tutorials nutzen stereotype Paketnamen wie service/, repository/ oder controller/. Frameworks wie Spring liefern UserService und UserController als Standardbeispiele; JPA-Tutorials produzieren am Fließband UserEntity.

Sogar moderne Ansätze wie Domain-Driven Design (DDD) oder Clean Architecture, die strategisch die Domänenorientierung predigen, schreiben auf taktischer Ebene oft starre Schichten und technische Artefakte vor.

Für die Lehre ist das legitim – sie will Technologie vermitteln, nicht Fachdomänen modellieren. Doch was als didaktische Vereinfachung beginnt, wird in der Praxis zur Blaupause. Entwickler übernehmen diese Strukturen in den Produktionscode, weil sie vertraut sind, nicht weil sie zum Problem passen. Was man zehnmal gesehen hat, fühlt sich wie ein Standard an.Die Konsequenz: Eine unsichtbare ÜbersetzungsschichtDadurch entsteht eine Kluft zwischen Business und IT. Wenn der Product Owner von „Zahlungsabwicklung“ spricht, denkt der Entwickler an den PaymentService. Meldet der Fachbereich ein Problem in der „Bestandsverwaltung“, sucht die IT im InventoryRepository.

Bei jeder Anforderung, jedem Meeting und jedem Bugticket findet im Kopf des Entwicklers eine stille Übersetzung statt – von der Sprache des Unternehmens in die Sprache der Architektur. Diese mentale Übersetzungsschicht kostet Zeit, provoziert Missverständnisse und bläht sich mit zunehmender Systemkomplexität immer weiter auf.

**Das Resultat ist eine paradoxe Abstraktionslücke:** Der Code beschreibt detailliert, wie das System gebaut ist, aber er verschweigt, was es eigentlich tut und für wen.

**Fazit:** Gute Namen sind keine Frage des persönlichen Geschmacks. Sie sind eine Frage der Design-Qualität. Die Präzision eines Namens ist der direkte Spiegel für die Präzision des gesamten Entwurfs.















## Das Problem hat Ursachen
Schlechte Namen entstehen nicht aus Nachlässigkeit, sondern aus Vorbildern und diese sind überall.

Dutzende Bücher und Tausende Blogbeiträge verwenden immer die selben Paketnamen wie service/, repository/, controller/, dto/. 
Frameworks wie Spring zeigt UserService, UserRepository, UserController. JPA-Tutorials produzieren UserEntity. 
Selbst moderne Ansätze wie Domain-Driven Design (DDD) und Clean Architecture, die strategisch Domänenorientierung predigen,
paradoxerweise auf taktischer Ebene starre Schichten und technische Artefakte vorschreiben. 

Das ist ihr legitimer Zweck, den sie wollen Technologie vermitteln und nicht die Fachdomänen modellieren. 
Doch was als didaktische Vereinfachung beginnt, wird zur Blaupause. Entwickler übernehmen diese Strukturen in Produktionscode, nicht weil sie durchdacht sind, sondern weil sie vertraut sind. Was zehnmal gleich aussah, fühlt sich wie ein Standard an.

In der Konsequenz, die weit über Code hinausgeht, entsteht eine mentale Übersetzungsschicht zwischen Business und IT.
Der Product Owner spricht über Zahlungsabwicklung, der Entwickler denkt an PaymentService. Der Fachbereich meldet ein Problem mit der Bestandsverwaltung, der Entwickler sucht in InventoryRepository. Bei jeder Anforderung, jedem Meeting, jedem Bugticket findet im Kopf des Entwicklers eine stille Übersetzung statt – von der Sprache des Unternehmens in die Sprache der Architektur. Diese Übersetzung kostet Zeit, erzeugt Missverständnisse und wächst mit der Komplexität des Systems.

So setzt sich die Abstraktionslücke auch dort fort, wo man sie am wenigsten erwartet: Der Code beschreibt, wie das System gebaut ist, nicht was es tut und für wen.
Gute Namen sind keine Frage des Stils. Sie sind eine Frage des Designs. Die Präzision eines Namens spiegelt die Präzision des dahinterliegenden Entwurfs wider.




Dabei ist das Paradoxe, dass selbst moderne Ansätze wie Domain-Driven Design (DDD) und Clean Architecture, die strategisch Domänenorientierung predigen, auf taktischer Ebene starre Schichten und technische Artefakte vorschreiben:

Das ist ihr legitimer Zweck, denn Sie wollen Technologie vermitteln, nicht Fachdomänen modellieren. 
Doch was als didaktische Vereinfachung beginnt, wird zur Blaupause.


 oft regiert im Code die „technische Bequemlichkeit“: Wir bauen UserService, OrderRepository und PaymentController, weil Framework-Tutorials es uns so vormachen.

Paket-, Klassen- und Methodennamen bilden gemeinsam die lesbare Oberfläche eines Systems. 
Die Paketstruktur definiert die Statik auf der Makroebene, Klassennamen bestimmen die Klarheit auf der Mikroebene, Methodennamen die Präzision auf der Interaktionsebene. 
Schlechte Namen auf jeder dieser Ebenen sind nicht nur ästhetisch störend – sie verraten ein grundlegend falsches Design.

Die Kosten schlechter Namen sind konkret. Ein neuer Entwickler, der OrderService öffnet und 40 Methoden vorfindet, braucht Stunden, um zu verstehen, welche Verantwortung dieses Objekt trägt, weil es keine hat. Eine Klasse UserAccountManager lässt sich kaum isoliert testen, weil ihr Umfang so groß ist, dass jeder Test die halbe Anwendung aufspannt. Eine Methode wie findByCustomerAndStatusAndCreatedAfter in einem Repository wächst weiter, weil niemand weiß, wo eine neue Abfrage sonst hingehören soll. Und wer in einer Paketstruktur aus service/, repository/ und controller/ nach der Zahlungslogik sucht, dem bleibt nur das Raten.

Das Problem hat eine Ursachen
Schlechte Namen entstehen nicht aus Nachlässigkeit. Sie entstehen aus Vorbildern, und diese Vorbilder sind überall.
Frameworks, Blogs und Tutorials erklären Technologie anhand technischer Beispiele. Spring zeigt UserService, UserRepository, UserController. JPA-Tutorials produzieren UserEntity. Dutzende Bücher und Tausende Blogbeiträge verwenden service/, repository/, controller/, dto/. Das ist ihr legitimer Zweck: Sie wollen Technologie vermitteln, nicht Fachdomänen modellieren. Doch was als didaktische Vereinfachung beginnt, wird zur Blaupause. Entwickler übernehmen diese Strukturen in Produktionscode, nicht weil sie durchdacht sind, sondern weil sie vertraut sind. Was zehnmal gleich aussah, fühlt sich wie ein Standard an.

Das hat eine Konsequenz, die weit über Code hinausgeht: Es entsteht eine mentale Übersetzungsschicht zwischen Business und IT. 

Der Product Owner spricht über Zahlungsabwicklung, der Entwickler denkt an PaymentService. Der Fachbereich meldet ein Problem mit der Bestandsverwaltung, der Entwickler sucht in InventoryRepository. Bei jeder Anforderung, jedem Meeting, jedem Bugticket findet im Kopf des Entwicklers eine stille Übersetzung statt – von der Sprache des Unternehmens in die Sprache der Architektur. Diese Übersetzung kostet Zeit, erzeugt Missverständnisse und wächst mit der Komplexität des Systems.

Diese mentale Übersetzungsschicht hat eine weitere, weniger offensichtliche Konsequenz: Die Implementierung entkoppelt sich zunehmend von der Domäne. Dabei ist das Paradoxe, dass selbst moderne Ansätze wie Domain-Driven Design (DDD) und Clean Architecture, die strategisch Domänenorientierung predigen, auf taktischer Ebene starre Schichten und technische Artefakte vorschreiben: Services, Usecases, Interactors, Repositories. Beide Werke genießen hohe Reputation und breite Anerkennung in der Branche. Diese Strukturen werden als Stand der Technik übernommen, weil sie aus Werken stammen, denen eine hohe methodische Autorität zugeschrieben wird. So setzt sich die Abstraktionslücke auch dort fort, wo man sie am wenigsten erwartet: Der Code beschreibt, wie das System gebaut ist, nicht was es tut und für wen.
Gute Namen sind keine Frage des Stils. Sie sind eine Frage des Designs. Die Präzision eines Namens spiegelt die Präzision des dahinterliegenden Entwurfs wider.
