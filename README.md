*Kopia strony autora z 23-09-2007 (http://web.archive.org/web/20071023225209/http://advajax.anakin.us/index-pl.htm)*

#AdvancedAJAX 1.1#

##001 Wstęp##

Poniższy tekst stanowi pełen spis metod i zmiennych obiektu AdvancedAJAX (advAJAX) w wersji 1.0. **Projekt ten jest dostępny za darmo zarówno do użytku prywatnego, jak i komercyjnego.** W celu otrzymania supportu należy skontaktować się z autorem (poprzez e-mail na adres umieszczony w prawej kolumnie).

Wykorzytywanie tego kodu poza założonym przeznaczeniem jest zabronione. Oznacza to, że można dołączyć kod źródłowy do swojego produktu, strony internetowej lub aplikacji czy każdej innej formy, w której kod będzie w rzeczywistości wykorzytywany. Zabronione jest natomiast udostępnianie kodu w postaci jawnej w internecie w postaci tekstu lub linka.

AdvancedAJAX jest obiektem wspomagającym wykorzystanie obiektu XMLHttpRequest i znacznie ułatwia wykonywanie zapytań AJAX. Zawiera on szereg metod wspomagających wykonywanie zapytań, obsługę błędów, wykorzystanie interfejsu w połączeniu z formularzami HTML, jak również przedawnianie czasu połączenia i jego wznawianie. AdvancedAJAX (jak również sam AJAX) jest w pełni kompatybilny z następującymi przeglądarkami internetowymi:

* Internet Explorer - 5.0 lub wyższy (oraz inne przeglądarki oparte na silniku Microsoftu)
* Mozilla - 1.0 lub wyższa (oraz inne przeglądarki oparte na Gecko)
* Apple Safari - 1.2 lub wyższy
* Opera - 7.60 lub wyższa

##002 Wykorzystanie##

AdvancedAJAX pozwala na proste i wygodne korzystanie z możliwości obiektu XMLHttpRequest. Posiada on zestaw metod umożliwiających wynikowanie zapytań typu GET, POST oraz HEAD, jak również przesyłanie treści formularzy.

Najprostszym sposobem jest wykorzystanie metod **get()**, **post()** oraz **head()**, w zależności od tego, jakiego typu zapytanie chcemy wysłać. Wszystkie z wymienionych metod przyjmują jeden parametr wywołania - obiekt zawierający parametry znane obiektowi i charakteryzujące zapytanie HTTP, obsługe błędów czy wznawianie połączenia podczas przekroczenia czasu oczekiwania. W przypadku, gdy przekazany parametr nie jest znany obiektowi, jest on traktowany jako argument zapytania HTTP i zostaje dodany do łańcucha zapytania (ang. query string).

Najprostsze wykorzystanie obiektu sprowadza sie do napisania kilku linijek kodu. Wykonanie poniższego kodu spowoduje pobranie treści dokumentu przekazanego w argumencie url i wyświetlenie jej w postaci okna dialogowego (globalna metoda **alert()**). Rezultat zapisywany jest w postaci tekstu w zmiennej **.responseText**, a w przypadku, gdy pobierany jest dokument XML, możemy wykorzystać zmienną **.responseXML**, która zawierać będzie obiekt DOM.

```
advAJAX.get({
    url: "http://www.example.com/page.html",
    onSuccess : function(obj) { alert(obj.responseText); }
});
```

Wykorzystanie w powyższym kodzie metody **post()** spowoduje jedynie zmianę typu wykonanego zapytania, rezultat będzie identyczny. Co jednak, jeśli podczas wykonywania zapytania wystąpi błąd, np. wywoływana strona nie będzie istnieć? Błędy się zdarzają, a ich obsługa za pomocą obiektu AdvancedAJAX sporowadza się do zarejestrowania funkcji dla metody **onError**. Kod błędu, np. **404** w przypadku nie znalezienia dokumentu pod zadanym adresem, zapisany jest w zmiennej **.status**, a jego opis w postaci tekstu (odpowiednio **Not found**) w **.statusText** (w Operze zawsze null).

```
advAJAX.get({
    url: "http://www.example.com/page.html",
    onSuccess : function(obj) { alert(obj.responseText); },
    onError : function(obj) { alert("Error: " + obj.status); }
});
```

W przypadku, gdy przekazana w **url** strona nie będzie istnieć, na ekranie wyświetlony zostanie komunikat **Error: 404**. AdvancedAJAX pozwala także na wywoływanie stron wymagających podania nazwy użytkownika i hasła. Za ich wartości odpowiedzialne są, kolejno, argument **username** i **password**. W przypadku podania błędnych danych logowania, wywołana zostanie również metoda **onError** z odpowiednim kodem HTTP zapisanym w zmiennej **.status**.

```
advAJAX.get({
    url: "http://www.example.com/page.html",
    username : "foo",
    password : "bar",
    onSuccess : function(obj) { alert(obj.responseText); },
    onError : function(obj) { alert("Error: " + obj.status); }
});
```

##003 Parametry i nagłówki##

Wykorzystując obiekt AdvancedAJAX można bez problemu ustawiać wartości argumentów wywołania zapytania i dodatkowych nagłówków, z nim przesyłanych. Dodanie parametrów odbywać się może na dwa sposoby - poprzez przekazanie ich w argumencie **parameters** lub bezpośrednio. W przypadku podania argumentu, który nie jest znany obiektowi AdvancedAJAX traktowany jest on jako parametr wywołania zapytania i dołączany do łańcucha zapytania.

```
advAJAX.get({
    url: "http://www.example.com/page.html",
    parameters : {
      "var1" : "value1",
      "var2" : "value2"
    },
    onSuccess : function(obj) { alert(obj.responseText); },
    onError : function(obj) { alert("Error: " + obj.status); },
    "var3" : "value3"
});
```

Wykonanie powyższego kodu spowoduje wykonanie zapytania **http://www.example.com/page.html?var1=value1&var2=value2&var3=value3**, ponieważ wszystkie parametry poza **var3** są znane obiektowi, także i on zostanie dołączony do listy parametrów zapytania. Wykorzystanie w tym miejscu metody **post()** spowoduje przesłanie argumentów w treści zapytania, zgodnie ze specyfikacją protokołu HTTP, co pozwala nam na bezproblemowe przesyłanie dużych porcji danych. Identyczne w skutkach będzie wywołanie poniższego kodu, z tym, że w tym przypadku jeden z argumentów został umieszczony bezpośrednio w argumencie **url**, drugi poprzez parametr **queryString**, a ostatni w **parameters**.

```
advAJAX.get({
    url: "http://www.example.com/page.html?var1=value1",
    queryString : "var2=value2",
    parameters : {
      "var3" : "value3"
    },
    onSuccess : function(obj) { alert(obj.responseText); },
    onError : function(obj) { alert("Error: " + obj.status); }
});
```

W przypadku, gdy chcemy przesłać dodatkowe nagłówki, możemy wykorzystać argument **headers** w identyczny sposób jak omówiony już **parameters**.

```
advAJAX.get({
    url: "http://www.example.com/page.html",
    headers : {
      "Custom-Header" : "value"
    }
    onSuccess : function(obj) { alert(obj.responseText); },
    onError : function(obj) { alert("Error: " + obj.status); }
});
```

##004 Ponawianie połączenia##

AdvancedAJAX posiada również mechanizmy odpowiedzialne za ponawianie połączenia w przypadku wystąpienia błędu. Dodanie tego jest niezwykle proste. Podczas przekazywania parametrów AdvancedAJAX należy utworzyć wartość dla pola **retry**, które określa liczbę prób ponownego połączenia. Możliwe, aczkolwiek opcjonalne, jest podanie wartości dla pola **retryDelay**, które określa liczbę milisekund pomiędzy próbami połączenia. Domyślną dla tego pola wartością jest 1000, czyli 1 sekunda. W przypadku wystąpienia błędu, przed próbą ponownego połączenia wywoływana jest metoda **OnRetry**, którą również możemy zmodyfikować. Przed zatrzymaniem działania skryptu na liczbę milisekund określoną w parametrze **retryDelay** wykonywana jest metoda **onRetryDelay**. Należy jednak pamiętać, że ponawianie połączenia następuje wyłączenie podczas przekroczonego czasu łączenia poprzedniej próby, a nie w przypadku wystąpienia przykładowo błędu 404 Not Found. Maksymalny czas łączenia definiujemy za pomocą parametru **timeout**, który winien zawierać liczbę milisekund. Po przekroczeniu czasu łączenia wywoływana jest metoda **onTimeout**, oczywiście przed **onRetry**.

```
advAJAX.get({
    url: "http://www.example.com/page.html",
    timeout : 3000,
    onTimeout : function() { alert("Connection timed out."); },
    retry: 2,
    retryDelay: 2000,
    onRetry : function() { alert("Retry connection..."); },
    onRetryDelay : function() { alert("Awaiting retry..."); },
    onSuccess : function(obj) { alert(obj.responseText); },
    onError : function(obj) { alert("Error: " + obj.status); }
});
```

Wykonanie powyższego kodu spowoduje ponownienie próby połączenia dwukrotnie (czyli w sumie skrypt będzie się łączyć maksymalnie trzy razy) w przypadku wystąpienia błędu. Odstępy pomiędzy próbami wynosić będą dwie sekundy, a pomiędzy połączeniami wykonane zostaną, w kolejności, metody **onRetryDelay** oraz **onRetry**. Maksymalny czas łączenia został tutaj ustawiony na trzy sekundy, a w przypadku jego przekroczenia, przed ponowną próbą, zostanie wywołana metoda **onTimeout**. Aby sprawdzić, czy wystąpiło przedawnienie łączenia bez wykorzystania **onTimeout**, wystarczy odwołać się do zmiennej **.aborted**, która w tym wypadku będzie miała wartość **true**.

##005 Pamięc tymczasowa##

Internet Explorer zapisuje w pamięci tymczasowej wywołania stron poprzez AJAX. Oznacza to, że nawet jeśli cokolwiek zostanie zmienione po stronie serwera, przeglądarka Microsoftu i tak skorzysta z lokalnie zapisanych danych. W tym celu AdvancedAJAX posiada dwa parametry, które rozwiązują ten problem. Parametr **unique** (domyślnie **true**) określa, czy do zapytania powinno być dodane pole, zawierające losowo generowaną wartość, co zapobiegnie zapisaniu jego rezultatu w pamięci tymczasowej. Można przy tym zdefiniować nazwę owego pola (domyślnie **"ajax_uniqid"**) za pomocą parametru **uniqueParameter**.

```
advAJAX.get({
    url: "http://www.example.com/page.html",
    uniqueParameter: "custom_unique_param",
    onSuccess : function(obj) { alert(obj.responseText); },
    onError : function(obj) { alert("Error: " + obj.status); }
});
```

##006 Pełna kontrola##

AdvancedAJAX zawiera zestaw dodatkowych modyfikowalnych akcji, które pozwalają na pełną kontrolę nad procesem łączenia, pobierania danych czy ewentualnych błędów. Pierwszą parą są **onInitialization** i **onFinalization**, obydwie wykonywane tylko raz. **onInitialization** wykonywana jest przed tworzeniem obiektu XMLHttpRequest, natomiast **onFinalization** po wykonaniu wszystkich zdefiniowanych akcji, włączając w to ponawianie połączenia czy wystąpienie błędu.

```
advAJAX.get({
    url: "http://www.example.com/page.html",
    onInitialization : function() {
        /* Ukrycie warstwy */
        someDiv.style.visibility = "hidden";
    },
    onSuccess : function(obj) {
        /* Ustawienie nowej, pobranej z serwera wartości */
        someDiv.innerHTML = obj.responseText;
    },
    onError : function(obj) {
        /* W przypadku wystąpienia błędu wyświetlenie komunikatu */
        alert("Error: " + obj.status);
    },
    onFinalization : function() {
        /* Ponowne pokazanie warstwy po zakończeniu wszystkich operacji */
        someDiv.style.visibility = "visible";
    }
});
```

Proces łączenia i pobierania danych, w przypadku XMLHttpRequest, składa się z czterech części. Wykonywane są w tym przypadku, kolejno, metody **onLoading**, **onLoaded**, **onInteractive** oraz **onComplete**. Ważny w tym miejscu jest fakt, że **onComplete** wykonywane jest bez względu na to, czy dane zostały pobrane pomyślnie, czy też wystąpił błąd - wykonywana jest więc zawsze przed **onSuccess** i **onError**. Jedynym wyjątkiem jest Opera, przy której nie wywoływana jest metoda **onLoaded**.

```
advAJAX.get({
    url: "http://www.example.com/page.html",
    onLoading : function(obj)
        { /* Wyświetlenie obrazka wskazującego na ładowanie */ },
    onComplete : function(obj) { /* Ukrycie obrazka... */ },
    onSuccess : function(obj) { /* Wyświetlenie pobranych danych */ }
});
```

W przypadku wystąpienia błędu krytycznego (np. podczas tworzenia obiektu XMLHttpRequest czy otwierania połączenia z adresem w innej domenie czy nieznanym protokole) wykonywana jest metoda **onFatalError**. Jeśli to wystąpi, skrypt natychmiast kończy działanie, wywołując przedtem jedynie **onFinalization**.

Poniżej przedstawiono w trzech kolumnach przykładowe wywołania definiowalnych akcji. Pierwsza kolumna to przykład pomyślnego wykonania połączenia i pobranie danych, druga przy wystąpienia błędu **404 Not Found**, a trzecia dla dwóch prób ponownego połączenia, ostatecznie zakończona porażką.

```
onInitialization        onInitialization        onInitialization
onLoading               onLoading               onLoading
onLoaded                onLoaded                onTimeout
onInteractive           onInteractive           onRetryDelay
onComplete              onComplete              onRetry
onSuccess               onError                 onLoading
onFinalization          onFinalization          onTimeout
                                                onRetryDelay
                                                onRetry
                                                onLoading
                                                onTimeout
                                                onFinalization
```

##007 Obsługa formularzy##

AdvancedAJAX posiada dwie funkcje wspomagające pracę z formularzami HTML. Pierwszą z nich jest **.submit()**, której wywołanie powoduje pobranie wartości wszystkich pól formularza i przesłanie ich pod adres URL jego akcji. Funkcja ta przyjmuje dwa argumenty. Pierwszym z nich jest wskaźnik do obiektu DOM formularza, czyli np. wynik działania funkcji **document.getElementById()**, drugim natomiast znana już lista argumentów AdvancedAJAX. W przypadku, gdy chcemy jedynie wysłać dane formularza, lista argumentów może pozostać pusta.

```
advAJAX.submit(document.getElementById("the_form"), {
    onSuccess : function(obj) { alert(obj.responseText); },
    onError : function(obj) { alert("Error: " + obj.status); }
});
```

Drugą z funkcji jest **.assign()**, która przyjmuje ten sam zestaw argumentów co poprzednio omawiana funkcja. Wywołanie tej funkcji z odwołaniem na wybrany formularz spowoduje, że AdvancedAJAX zostanie uruchomiony dopiero podczas naciśnięcia przycisku wysłania formularza (**<input type="submit" ... />**) lub każdej innej akcji, która wywoływała by standardowe procedury wysyłania formularza. Ważnym faktem jest to, że dotychczasowa metoda **onsubmit** formularza, z której korzysta AdvancedAJAX, zostaje zapamiętana i mimo wszystko wywołana. W przypadku, gdy wystąpi krytyczny błąd, AdvancedAJAX przerywa działanie, a formularz zostaje wysłany w standardowy sposób. Reasumując, aby wykorzystać AdvancedAJAX do wysłania danych formularza w tle wystarczy tylko wywołać metodę **.assign()**, przy czym można opcjonalnie podać dodatkowe argumenty AdvancedAJAX zapobiegające przedawnieniu czasu połączenia czy obsługujące rezultat wywołania adresu URL.

```
advAJAX.assign(document.getElementById("the_form"), {});
```

Aby zapobiec wielokrotnemu naciśnięciu przycisku powodującego wysłanie danych formularza, AdvancedAJAX posiada wbudowaną funkcję blokującą wszystkie pola formularza do czasu zakończenia wywołania, po czym zostaną one przywrócone do pierwotnego stanu. Blokowanie jest domyślną akcją, wywoływaną po dołączeniu obiektu do formularza za pomocą metody **.assign()** i definiowalnej za pomocą parametru **disableForm**. Poniższy kod spowoduje dołączenie AdvancedAJAX do formularza, przy czym podczas przesyłania danych jego kontrolki nie będą blokowane.

```
advAJAX.assign(document.getElementById("the_form"), {
   disableForm : false
});
```

##008 Grupowanie wywołań##

W przypadku wykonywania kilku wywołań HTTP jednocześnie nie mamy kontroli nad tym, które z nich rozpocznie się jako pierwsze, a które zakończy jako ostatnie... jednak nie w przypadku AdvancedAJAX. Wykorzystując definiowalne metody **onGroupEnter** i **onGroupLeave** w połączeniu z parametrem ** group**, dają nam tę możliwość. W praktyce oznacza to, po pogrupowaniu wywołań będziemy wiedzieć kiedy wszystkie z nich zakończyły działanie. Poniższy kod wykorzystuje statyczną metodę **setDefaultParameters** o której przeczytać można w następnym rozdziale.

```
advAJAX.setDefaultParameters({
    group : "sample_group",
    onGroupLeave : function() { alert("Everything loaded..."); },
    onSuccess : function(obj) { alert(obj.responseText); },
    onError : function(obj) { alert("Error: " + obj.status); }
});
advAJAX.get({ url: "http://www.example.com/page1.html" });
advAJAX.get({ url: "http://www.example.com/page2.html" });
advAJAX.get({ url: "http://www.example.com/page3.html" });
```

##009 Dodatki##

W poprzednim rozdziale wykorzystana była funkcja **setDefaultParameters**. Jej wywołanie powoduje automatyczne dodanie parametrów do każdego, występującego po niej, wywołania metod **.get()**, **.post()**, **.head()**, **.submit()** oraz **.assign()**. Pozwala nam to na uniknięcie kilkukrotnego pisania tych samych argumentów, włączając w to modyfikowalne metody, których wielokrotne użycie w różnych wywołaniach powoduje tworzenie co raz to nowej funkcji. Dzięki **setDefaultParameters** można nie tylko zoptymalizować działanie AdvancedAJAXa, ale również wprowadzić czytelność w pisanym kodzie.

Bardzo prostą, a jednocześnie użyteczną właściwością obiektu AdvancedAJAX jest parametr **tag**, który zawierać może dowolną wartość tekstową, liczbową czy jakąkolwiek inną. Jego modyfikacja nie wpływa na sam proces połączenia, a jedynie pozwala na przechowanie dowolnego typu danych wewnątrz obiektu. Poniżej przedstawiony został prosty przykład wykorzystania tego parametru. Zapisany w nim został id obiektu, do którego wpisane mają być pobrane dane z trzech różnych wywołań.

```
advAJAX.setDefaultParameters({
    onSuccess : function(obj) {
    
        document.getElementById(obj.tag).innerHTML = obj.responseText;
    }
});
advAJAX.get({ tag: "layer1", url: "http://www.example.com/page1.html" });
advAJAX.get({ tag: "layer2", url: "http://www.example.com/page2.html" });
advAJAX.get({ tag: "layer3", url: "http://www.example.com/page3.html" });
```

##010 Przykłady##
