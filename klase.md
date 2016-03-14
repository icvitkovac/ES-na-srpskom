# Klase



Kako su klase nov pojam u JS svetu, ne bi bilo loše da se vratimo osnovama i podsetimo čemu klase uopšte služe u programiranju. Oni koji pozadinu vuku iz drugih programskih jezika, slobodno mogu da preskoče narednu sekciju. 

####Istorija i osnovni pojmovi#### 

Svi smo imali priliku da u osnovnoj školi na časovima biologije crtamo ili bar gledamo drvo života na Zemlji. Deo kome mi pripadamo (sisari) predstavlja jednu klasu na tom drvetu. Klasa sisara se dalje deli na potklase, koje se kasnije dele na redove (majmuni), a te redove sačinjavaju porodice, kojima pripadaju i ljudi. Radi lakšeg objašnjenja, možemo pogledati [pojednostavljenu strukturu](http://sr.wikipedia.org/wiki/%D0%9A%D0%BB%D0%B0%D1%81%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%98%D0%B0_%D1%81%D0%B8%D1%81%D0%B0%D1%80%D0%B0) ispod:

* Sisari
   * Kopnene životinje
        * Majmuni
            * Ljudi
            * Čovekoliki majmuni (šimpanze, gorile...)


Klase u programiranju vode poreklo upravo odavde i predstavljaju pokušaj da se problemi iz realnog sveta predstave u računarskom kodu na nama razumljiv način. 

Klase omogućavaju da u programiranju koristimo koncept nasleđivanja; ako se vratimo na prethodni primer, možemo uočiti da ono što sisare izdvaja od ostalih životinja je upravo ono što im ime kaže - sisaju mleko. Zato znamo zasigurno da sve životinje koje pripadaju sisarima moraju sisati mleko. Dakle, sve životinje *nasleđuju* to ponašanje od klase sisara.

Isti princip se nastavlja kako se spuštamo dole niz piramidu - potklasi kopnenih životinja je, naravno, zajedničko to da žive na kopnu. Kako nastavljamo dalje, imamo sve više nasleđivanja od gornjih nivoa. Prenešeno u kod, ovaj princip omogućava razumljivije predstavljanje podataka i problema i jasniji i kraći kod.

#Otkud odjednom klase gde im mesto nije, kako bi neki rekli?# 

JavaScript je u prethodnoj deceniji izašao iz okvira korišćenja isključivo u Internet pretraživačima. Uz dodatnu pomoć u vidu prisilne eutanazije nad Flash okruženjem, veliki broj programera različitih orijentacija odlučio se upravo za JS. Sa povećanjem broja "programera dođoša" koji su videli beli svet, počeli su da nastaju prirodni pritisci na ostatak zajednice da se usvoje dobre prakse koje već dugo funkcionišu u "regularnim" programskim jezicima. Jedna od oblasti koja je posebno bola oči su naravno klase. 

Klase u ES6 su i dalje uglavnom kozmetička promena. Da, naravno, većini ljudi će život biti lakši jer će kod biti čitljiviji, razumljiviji i kraći, ali to nije jedini cilj. Sadašnja implementacija će omogućiti dodatno približavanje dobrim praksama iz objektno-orijentisanih programskih jezika. Sve je još u fazi nacrta, ali planovi za ES7 su vrlo obećavajući. 

#Klase u JavaScript-u# 

##class i constructor## 


Primer najjednostavnije klase:

```
class Vozilo {
  constructor(tipVozila){
    this.tipVozila = tipVozila;
  }
  opisVozila(){
    return 'Ovo vozilo je ' + this.tipVozila;
  }
}
```
Ovaj kod bi u ES5 izgledao ovako:

```
var Vozilo = (function () {
    function Vozilo(tipVozila) {
        this.tipVozila = tipVozila;
    }
    Vozilo.prototype.opisVozila = function () {
        return 'Ovo vozilo je ' + this.tipVozila;
    };
    return Vozilo;
})();

```

Kada uporedimo ova dva bloka, vidimo da se u prvom nigde ne pominje ključna reč **function**, jer se u okviru klase funkcije deklarišu na kraći način. Znamo da kod radi istu stvar, u približno istom broju linija koda, ali šta je drugačije?

Hajde da analiziramo prvi blok. Vidimo da su za sada jedine 2 nove stvari **class** i **constructor**.

Kada se klasa *instancira*, **constructor** funkcija se automatski poziva. Instanciranje se vrši na isti način kao što je to bio slučaj u ES5:

```
var automobil = new Vozilo('automobil');
var motocikl = new Vozilo('motocikl');
```

Pozivom metode *opisVozila* koje su instance klase nasledile, dobijamo sledeći izlaz:

```
automobil.opisVozila()
//"Ovo vozilo je automobil"
motocikl.opisVozila();
//"Ovo vozilo je motocikl"
```

Dakle, možemo zaključiti da instance klase nasleđuju sve metode koje se nalaze u roditeljskoj klasi. Ali, šta ako nam to ponašanje ne odgovara uvek, t.j. šta ako želimo da nešto ostane isključivo u klasi?

Upravo iz tog razloga postoji ključna reč **static**, koja kada se upotrebljava kao prefiks metode u okviru klase, označava da ta metoda treba da pripada samo klasi. 

##static## 


Recimo da smo u našem primeru počeli da se bavimo uvozom i prodajom raznih vrsta vozila (gliseri, automobili, kombiji) i da sarađujemo samo sa jednim prodavcem iz Češke. Naše krajnje kupce ne zanima koji je to prodavac, niti želimo to da im otkrijemo. 

Postojeći kod sada izgleda ovako:
```
class Vozilo {
    constructor(tipVozila){
        this.tipVozila = tipVozila;
    }

    /*METODE KLASE(A)*/

    static voziloKupljenoOd(){
        return 'Jaček i sinovi - Zeleneč, Prag';
    }

    /*METODE KOJE INSTANCE NASLEĐUJU*/

    opisVozila(){
        return 'Ovo vozilo je ' + this.tipVozila;
    }
}
```
Ako pokušamo da pozovemo ovu metodu iz instance klase:

```
var automobil = new Vozilo('automobil');
automobil.voziloKupljenoOd();
```
Vidimo da se javlja greška:

```
Uncaught TypeError: automobil.voziloKupljenoOd is not a function
```

//Dodati još par redova

Kroz praksu, kako smo naručivali jedno po jedno vozilo, primetili smo da nam je klasa veoma siromašna i da većinu stvari pišemo po nekim 'artijama. Postalo nam je očigledno da svako vozilo, bez izuzetka, ima svog proizvođača, model i godinu proizvodnje. Zato smo odlučili da obogatimo klasu tim podacima. 

```
class Vozilo {
    constructor(tipVozila, godiste, proizvodjac, model){
        this.tipVozila = tipVozila;
        this.godiste = godiste;
        this.proizvodjac = proizvodjac;
        this.model = model;
    }

    static voziloKupljenoOd(){
        return 'Jaček i sinovi - Zeleneč, Prag';
    }

    opisVozila(){
        return this.tipVozila + ' marke ' + this.proizvodjac +
        ' ' + this.model;
    }
}
```
Sada metoda opisVozila daje malo bolje informacije:

```
var automobil1 = new Vozilo('Automobil', 2004, 'Mazda', '6');
var automobil2 = new Vozilo('Automobil', 2007, 'Ford', 'Focus');

automobil1.opisVozila();
//"Automobil marke Mazda 6"
automobil2.opisVozila()
//"Automobil marke Ford Focus"
```


Kako naš posao raste, primetili smo da je malo besmisleno i ne preterano učinkovito da za svaki automobil moramo da unosimo naziv 'Automobil', a bacivši pogled na izveštaj o prodaji, jasno možemo videti da prodajemo najviše automobila. 

Vreme je da uradimo jedinu logičnu stvar u ovom trenutku - da automobile prebacimo u posebnu klasu. 

##extends## 

Sintaksa za ovo je:

```
class Automobil extends Vozilo {
  
}
```
Ovakva vrsta klase naziva se *izvedena klasa*.

Kao što se da predpostaviti, ovaj princip nam omogućava da dalje definišemo osobine klase. Hajde da razmislimo za trenutak šta definiše sve automobile. Znamo da svaki automobil na svetu ([sem izuzetka koji potvrđuje pravilo](http://onlyfoolsandhorses.wikia.com/wiki/Reliant_Regal)) ima 4 točka. Naravno, svi automobili dele i isto ime. Sa druge strane, stvari po kojima se dva auta međusobno razlikuju su naravno brojne - to može biti broj vrata, broj sedišta, tip menjača, pogon... 

Sve pomenute stvari pripadaju upravo podklasi, tj. na tom nivou ih treba definisati, jer nemaju sva vozila vrata niti različit broj sedišta (motocikl), niti mogu imati više vrsta pogona (gliser). 

Dakle, kao i do sada, držaćemo se ključnih zajedničkih osobina i razlika i pokušaćemo da definišemo našu novu izvedenu klasu na sledeći način:

```
class Automobil extends Vozilo {
//Kod nije pogodan za kopiranje jer sadrži grešku

    constructor(brojVrata, brojSedista, pogon) {
        const tipVozila = 'Automobil';
        const brojTockova = 4;
        //TODO dodati link ka članku o konstantama, ako dolazi kasnije u sadržaju

        this.brojVrata = brojVrata;
        this.brojSedista = brojSedista;
        this.pogon = pogon;
    }

    opisVozila() {
        //opet vraća neki opis
    }
}
```

Kada pogledamo kod, odmah vidimo da tu nešto nije baš sve kako treba. OK, jasno nam je da je klasa Automobil izvedena iz klase Vozilo, ali šta je sa podacima o proizvođaču, modelu...? Kako njih koristimo u izvedenoj klasi?

Kod iznad bi vratio sledeću grešku:

```
'this' is not allowed before super()
```

Upravo to nas dovodi do sledeće ključne reči - **super**. 

##super##

Super predstavlja super-konstruktor, odnosno nad-konstruktor roditeljske klase. Na ovaj način nam je omogućeno da izvedenu klasu instanciramo koristeći argumente koje bi prosledili i roditeljskoj klasi.

Ispravkom koda iznad, dobijamo sledeće:

```
class Automobil extends Vozilo {


    constructor(brojVrata, brojSedista, pogon, godiste, proizvodjac, model) {
        const tipVozila = 'Automobil';
        const brojTockova = 4;
        super(godiste, proizvodjac, model);
        this.tipVozila = tipVozila;
        this.brojTockova = brojTockova;
        this.godiste = godiste;
        this.proizvodjac = proizvodjac;
        this.model = model;
        this.brojVrata = brojVrata;
        this.brojSedista = brojSedista;
        this.pogon = pogon;
    }

    opisAutomobila() {
        return super.opisVozila() + ' ima ' + this.brojVrata + ' vrata i ' + this.pogon + ' pogon.'
    }
}
```

//

```
var mazda = new Automobil(5, 5, 'prednji', 1980, 'mazda', '6');
mazda.opisAutomobila(); // "Automobil marke mazda 6 ima 5 vrata i prednji pogon."
mazda.opisVozila(); // "Automobil marke mazda 6"
```

