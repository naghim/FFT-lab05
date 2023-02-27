# Labor 05

### Fájlkezelés

A fájlkezelés Qt-ben hasonlóan történik, mint vanilla C++-ban. Például egy szöveges fájl olvasásra való megnyitása (és olvasása) a következőképp történik:
```c++
QFile file(filename);
if(!file.open(QIODevice::ReadOnly | QIODevice::Text))
    {
        QMessageBox::warning(0, "Error", file.errorString());
        return;
    }
QTextStream in(&file);
QString myText = in.readAll();
...
file.close();
```

### Események filterezése

Néha egy objektumnak meg kell néznie, és esetleg elfognia azokat az eseményeket, amelyek egy másik objektumhoz érkeznek. Például a párbeszédablakokba beírt input esetén szeretnénk kiszűrni bizonyos gombnyomásokat (számokat, betűket, speciális billentyűket). A ```QObject::installEventFilter(QObject *filterObj)``` függvény lehetővé teszi ezt egy eseményszűrő beállításával, aminek hatására egy kijelölt szűrőobjektum fogadja az eseményeket a ```QObject::eventFilter(QObject *object, QEvent *event)``` függvényében (_használatkor ezt kell majd felülírni_). Az eseményszűrő az eseményeket még a célobjektum **előtt** feldolgozza, lehetővé téve az események ellenőrzését és szükség szerinti elvetését (amennyiben a függvény visszatérítési értéke igaz). Egy meglévő eseményszűrő eltávolítható a ```QObject::removeEventFilter(QObject *obj)``` függvény segítségével. Amennyiben minden beállított szűrő hamis értékkel tér vissza, a célobjektum is megkapja az eseményt, ellenkező esetben a legelső igaz értékkel visszatért szűrő után a többi szűrő sem kapja meg az eseményt.
Például: 
```c++
bool FilterObject::eventFilter(QObject *object, QEvent *event)
{
    if (event->type() == QEvent::KeyPress) {
        QKeyEvent *keyEvent = static_cast<QKeyEvent *>(event);
        if( (keyEvent->key() == Qt::Key_Enter) || (event->key() == Qt::Key_Return))
            // ha Enter billentyű került lenyomásra
            ...
            return true;
    }
    return false;
}
```

## Feladatok

1. Készítsünk egy alkalmazást, amelyben egy szavakból álló listát jelenítünk meg, és egy szövegdoboz segítségével szűrhetjük a tartalmat (használjunk ```QLineEditet``` és annak a ```textEdited(const QString &text)``` eseményét, dokumentáció [itt](https://doc.qt.io/qt-5/qlineedit.html#textEdited)). A szavakat egy tetszőlegesen, a felhasználó által kiválasztott szöveges fájlból töltsük be. Az ablakot lehessen átméretezni és a tartalom alkalmazkodjon az új mérethez. 

![1](https://user-images.githubusercontent.com/78269344/109413419-f2886a80-79b5-11eb-8a30-90bfc35db7a4.png)

2. Implementáljuk a „6 másodperces kihívás” játékot, melyben a felületen egy nagyfelbontású óra számol fel 0-tól 6 másodpercig. A játékos célja az, hogy egy gomb lenyomásával, minél közelebb állítsa meg az órát a 6 másodperchez. A legjobb 10 eredményt, top 10 ranglistát (helyezés, név, idő) tároljuk egy fájlban. A felhasználó kérésére ezt a program tudja megjeleníteni. 
Használjuk a [QTimert](https://doc.qt.io/qt-5/qtimer.html) ütemezésre és [QInputDialog](https://doc.qt.io/qt-5/qinputdialog.html) párbeszédablakot a nevek bekérésére, mikor az elért idővel a felhasználó felkerül a ranglistára. 
 
3. Készítsünk egy alkalmazást mely egy grafikus felületen keresztül bekéri a nevünket és telefonszámukat és ezeket elmenti egy szöveges állományba. Függőség befecskendezéssel, a void ```QObject::installEventFilter(QObject *filterObj)``` metódust felhasználva, installáljunk egy monitor objektumot, mely csak számjegyek beírását engedélyezi a telefonszám szövegmezőben.  

4. Az előző feladat adatbázisához készítsünk egy kereső alkalmazást, melyben név és/vagy telefonszám szerint lehet keresni. 

