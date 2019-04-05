# Calendar server app

Example app for purpose of WarsawJS workshop #31

## `API`

#### `GET: /api/calendar?month={YYYY-MM}`
##### Fetches calendar month
###### `response`

```
{
  data: [
    date: string(format=YYYY-MM-DD),
    events: [
      {
        id: string(format=guid)
        title: string
      }
    ]
  ]
}
```

#### `GET: /api/day?date={YYYY-MM-DD}`
##### Fetches calendar day
###### `response`

```
{
  data: [
    {
      id: string(format=guid)
      title: string
      description: string
      time: string(format=YYYY-MM-DDThh:mm)
      notification: boolean
    }
  ]
}
```


#### `POST: /api/event`
##### Creates event
###### `body`

```
{
  title: string
  description: string
  time: string(format=YYYY-MM-DDThh:mm)
  notification: boolean
}

```

###### `response:status`

```
{
  id: string
}
```


#### `PUT: /api/event/:id`
##### Updates event
###### `body`

```
{
  title: string
  description: string
  time: string(format=YYYY-MM-DDThh:mm)
  notification: boolean
}

```

###### `response:status`

```
{
  id: string
}
```


#### `DELETE: /api/event/:id`
##### Deletes reminder
###### `body`

```
{
  id: string
}

```

###### `response:status`

```
{
  id: string
}
```


#### `POST: /api/subscriptions`
##### Register user subscription
###### `body`

```
{
  data: {
    endpoint: URL
    expirationTime: Date
    keys: { 
      p256dh: string
      auth: string
    }
  }
}

```
###### `response:status`

```
201 || resource create
```

## Opis etapów szkolenia
### Etap 1 - początek pracy - routing

Serwer w tym stanie serwuje pliki statyczne z folderu `public`.
Po uruchomieniu serwera, aplikację można zobaczyć w przeglądarce pod adresem `localhost:5000`.
Pierwszym zadaniem, będzie dodanie routingu do serwera.
Na podstawie kontraktu API, opisanego w pliku README na branchu master, dodać należy wszystkie endpointy wykorzystywane przez klienta.
Na razie nie mamy danych, więc będziemy zwracać zamockowane dane w postaci odpowiadającej projektowi `api`.
Wszystkie endpointy warto zgrupować przy pomocy wspólnego routera.
Po prawidłowym dodaniu endpointów, nie powinniśmy widzieć błędów w konsoli Network w przeglądarce.

[Rozwiązanie tego etapu](https://github.com/G3F4/warsawjs-workshop-31-calendar/compare/workshop...etap-1)

### Etap 2 - logowanie

Do logowania wykorzystamy `OAuth` ze strategią `GitHub`.
Potrzebne będą zależności `passport` oraz `passport-github`.
Rozwiązanie jjest oparte na sesji więc należy dodać do `express` obsługę sesji.
Wykorzystamy do tego zależność `express-session`.

[Rozwiązanie tego etapu](https://github.com/G3F4/warsawjs-workshop-31-calendar/compare/etap-1...etap-2)

### Etap 3 - baza danych

Do przechowywania danych użyjemy `MongoDB`. Jako sterownik posłuży nam `mongoose`.
Do pracy będziemy potrzebować 2 modeli i schem - `UserModel` oraz `EventModel`.
Wszystkie operacje na kolekcjach będziemy wykonywać przez moduł proxy `api`. 
Dzięki temu warstwa bazy danych zostanie wyraźnie odcięta od domeny serwera.
Następnie wykorzystamy nowe `api` do integracji z `apiRouter`.

[Rozwiązanie tego etapu](https://github.com/G3F4/warsawjs-workshop-31-calendar/compare/etap-2...etap-3)

### Etap 4 - powiadomienia - obsługa rejestracji subskrycji

Teraz dodamy do aplikacji obsługę powiadomień przy wykorzystaniu `web-push` - zależność należy dodać do projektu.
Do prawidłowego funkcjonowania wymagane jest wygenerowanie poprawnych kluczy VAPID.
https://www.npmjs.com/package/web-push#using-vapid-key-for-applicationserverkey
Aplikacja kliencka wysyła obiekt rejestracji subskrycji za każdym razem gdy zainstaluje albo zauktualizuje service worker.
Aby wywołać w prosty sposób instalację SW najlepiej zatrzymać aktywny SW i przeładować stronę.
Za każdym razem gdy serwer otrzyma subskrycję powinień wysłać powiadomienie powitalne i zapisać subskrycję w bazie.

[Rozwiązanie tego etapu](https://github.com/G3F4/warsawjs-workshop-31-calendar/compare/etap-3...etap-4)

### Etap 5 - cykliczne wysyłanie powiadomień

Na koniec zadbamy aby do każdego wydarzenia w kalendarzu, które ma aktywne powiadomienie, zostało wysłane powiadomienie.
Zadbać należy aby powiadomienia były jednorazowe - jedno per wydarzenia.
Do stworzenia joba wykorzystamy `agenda`, którą należy dodać do zależności.
Następnie musimy uruchomić środowisko oraz zdefiniować job do wykonania oraz określić jego cykl.

[Rozwiązanie tego etapu](https://github.com/G3F4/warsawjs-workshop-31-calendar/compare/etap-4...etap-5)
