# Labor 05

## Események Qt-ben

A Qt keretrendszerben az eseményeket többféleképpen lehet kezelni és szűrni az alkalmazás igényeinek megfelelően (dokumentáció [itt](https://doc.qt.io/qt-6/eventsandfilters.html)). A leggyakrabban használt módja az eseménykezelők használata, amelyek segítségével a Qt alkalmazások reagálhatnak a felhasználói interakciókra és más eseményekre.

### Eseménykezelők

Minden `QObject` leszármazottban (például a `QWidget`, vagy annak további leszármazottjában: `MyWidget`) van lehetőség új, illetve az örökölt eseménykezelők definiálására. Ezek a függvények kezelik az eseményeket, amelyeket az adott objektum kap meg. Például:

```c++
class MyWidget : public QWidget {
protected:
    void keyPressEvent(QKeyEvent *event) override {
        if (event->key() == Qt::Key_Escape) {
            // Ha a felhasználó ESC-et nyomott
            event->accept(); // Azt mondjuk, hogy lekezeltük az eseményt és nem propagáljuk tovább
        } else {
            QWidget::keyPressEvent(event); // Hívjuk meg az ős osztályt az alapértelmezett viselkedésért
        }
    }
};
```

### Event Filter

Az események szűrésének egy másik módja, amely lehetővé teszi az események globális szintű figyelését: egy központi helyen, például egy esemény szűrő objektumban, figyeljük az összes eseményt, amelyet az alkalmazásban lévő objektumok fogadnak. Például a párbeszédablakokba beírt input esetén szeretnénk kiszűrni bizonyos gombnyomásokat (számokat, betűket, speciális billentyűket stb.). Ehhez létre kell hozni egy saját `QObject`-ből származott osztályt, és felül kell írni az `QObject::eventFilter(QObject *object, QEvent *event)` metódust:

```c++
class MyFilterObject : public QObject {
    Q_OBJECT
protected:
    bool eventFilter(QObject *obj, QEvent *event) override {
        if (event->type() == QEvent::KeyPress) {
            QKeyEvent *keyEvent = static_cast<QKeyEvent*>(event);
            if (keyEvent->key() == Qt::Key_Escape) {
                // Ha a felhasználó ESC-et nyomott
                return true; // Azt mondjuk, hogy lekezeltük az eseményt
            }
        }
        return QObject::eventFilter(obj, event); // Átadás az alapértelmezett eseménykezelésnek
    }
};
```

A szűrőt a `QObject::installEventFilter(QObject *filterObj)` függvénnyel lehet feliratkoztatni:

```c++
MyFilterObject *myFilter = new MyFilterObject;
QLineEdit *phoneEdit = new QLineEdit;

phoneEdit->installEventFilter(myFilter);
```

Az eseményszűrő az eseményeket még a célobjektum **előtt** feldolgozza, lehetővé téve az események ellenőrzését és szükség szerinti elvetését (amennyiben a függvény visszatérítési értéke `true`, magyarán az eseményt feldolgoztuk, nem szükséges továbbpropagálni).

Egy meglévő eseményszűrő eltávolítható a `QObject::removeEventFilter(QObject *obj)` függvény segítségével. Amennyiben minden beállított szűrő hamis értékkel tér vissza, a célobjektum is megkapja az eseményt, ellenkező esetben a legelső igaz értékkel visszatért szűrő után a többi szűrő sem kapja meg az eseményt.

## Feladatok

1. Készítsünk egy alkalmazást, amelyben egy szavakból álló listát jelenítünk meg, és egy szövegdoboz segítségével szűrhetjük a tartalmat (használjunk `QLineEditet` és annak a `textEdited(const QString &text)` eseményét, dokumentáció [itt](https://doc.qt.io/qt-5/qlineedit.html#textEdited)). A szavakat egy tetszőlegesen, a felhasználó által kiválasztott szöveges fájlból töltsük be. Az ablakot lehessen átméretezni és a tartalom alkalmazkodjon az új mérethez.

![1](https://user-images.githubusercontent.com/78269344/109413419-f2886a80-79b5-11eb-8a30-90bfc35db7a4.png)

2. Készítsünk egy grafikus felhasználói felülettel rendelkező alkalmazást, amely lehetővé teszi a felhasználók számára, hogy megadjanak egy nevet és egy telefonszámot, majd ezeket az adatokat elmenti egy szöveges állományba. A telefonszám szövegdobozába csak számjegyeket lehet beírni. Ehhez használjunk második módszert, vagyis az `Object::installEventFilter(QObject *filterObj)` metódust, hogy csak számokat lehessen a telefonszám szövegdobozába beírni.

3. Készítsünk egy Notepad alkalmazást, amely egy szövegdobozból áll. A szövegdobozhoz használjunk egy saját osztályt, amelyet a [`QPlainTextEdit`](https://doc.qt.io/qt-6/qplaintextedit.html) osztályból származtassunk. Tegyük lehetővé a következő funcionalitásokat:

   1. Ha megnyomjuk a `Ctrl+S` billentyűkombinációt, akkor mentse el a szövegdoboz tartalmát az `output.txt` állományba. Írjuk felül a `keyPressEvent` függvényt.

   2. Csempésszük bele a német betűkészletet billentyűkombinációk lenyomásával:

   - `Alt+A` lenyomása esetén jelenjen meg egy `ä` betű
   - `Alt+O` lenyomása esetén jelenjen meg egy `ö` betű
   - `Alt+U` lenyomása esetén jelenjen meg egy `ü` betű
   - `Alt+S` lenyomása esetén jelenjen meg egy `ß` betű
   - elég csak a kisbetűket leimplementálni.
