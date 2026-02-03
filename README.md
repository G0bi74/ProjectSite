# ProjectSite

## Opis projektu

Ten projekt został stworzony jako zadanie mające na celu rozwinięcie umiejętności programowania. Zawiera system uwierzytelniania użytkowników (authentication) zaimplementowany w języku Java z wykorzystaniem bazy danych SQLite.

## Technologie

Projekt wykorzystuje następujące technologie i narzędzia:

- **Java 20** - język programowania
- **Maven** - narzędzie do zarządzania projektem i zależnościami
- **SQLite** - lekka baza danych SQL (wykorzystana biblioteka: `sqlite-jdbc 3.41.2.1`)
- **BCrypt** - biblioteka do bezpiecznego hashowania haseł (`bcrypt 0.10.2`)

## Struktura projektu

```
ProjectSite/
├── site/                          # Główny katalog projektu
│   ├── src/
│   │   └── main/
│   │       └── java/
│   │           ├── auth/          # Moduł uwierzytelniania
│   │           │   ├── Account.java         # Klasa reprezentująca konto użytkownika
│   │           │   └── AccountManager.java  # Zarządzanie kontami i uwierzytelnianiem
│   │           └── database/      # Moduł bazy danych
│   │               └── DatabaseConnection.java  # Połączenie z bazą SQLite
│   ├── pom.xml                    # Konfiguracja Maven
│   └── shop.db                    # Baza danych SQLite
└── README.md                      # Ten plik
```

## Funkcjonalności

### 1. Zarządzanie kontami (`AccountManager`)

Klasa `AccountManager` oferuje następujące funkcje:

- **Rejestracja użytkownika** (`register`) 
  - Tworzy nowe konto z hashowanym hasłem
  - Sprawdza czy użytkownik już istnieje
  - Wykorzystuje BCrypt do bezpiecznego hashowania haseł (cost factor: 12)

- **Logowanie** (`login`)
  - Weryfikuje dane uwierzytelniające użytkownika
  - Porównuje podane hasło z zahashowanym hasłem w bazie danych
  - Zwraca `true` jeśli logowanie się powiodło, w przeciwnym razie `false`

- **Pobieranie konta** (`getAccount`)
  - Pobiera informacje o użytkowniku na podstawie nazwy

### 2. Model konta (`Account`)

Prosta klasa rekordowa (Java record) przechowująca:
- `id` - identyfikator użytkownika
- `name` - nazwa użytkownika

### 3. Połączenie z bazą danych (`DatabaseConnection`)

Klasa zarządzająca połączeniem z bazą SQLite:
- `connect(String path)` - nawiązuje połączenie z bazą danych
- `disconnect()` - zamyka połączenie
- `getConnection()` - zwraca obiekt Connection do wykonywania zapytań SQL

## Struktura bazy danych

Projekt wykorzystuje bazę SQLite z tabelą `auth_account` zawierającą:
- `id` - klucz główny
- `name` - nazwa użytkownika (unikalna)
- `password` - zahashowane hasło (BCrypt)

## Jak uruchomić projekt

### Wymagania
- Java 20 lub nowsza
- Maven 3.x

### Budowanie projektu

```bash
cd site
mvn clean install
```

### Używanie w kodzie

```java
// Połączenie z bazą danych
DatabaseConnection dbConnection = new DatabaseConnection();
dbConnection.connect("shop.db");

// Utworzenie managera kont
AccountManager accountManager = new AccountManager(dbConnection);

// Rejestracja nowego użytkownika
try {
    int userId = accountManager.register("jankowalski", "bezpieczneHaslo123");
    System.out.println("Zarejestrowano użytkownika z ID: " + userId);
} catch (SQLException e) {
    e.printStackTrace();
}

// Logowanie
try {
    boolean success = accountManager.login("jankowalski", "bezpieczneHaslo123");
    if (success) {
        System.out.println("Logowanie pomyślne!");
    } else {
        System.out.println("Błędne dane logowania!");
    }
} catch (SQLException e) {
    e.printStackTrace();
}

// Zamknięcie połączenia
dbConnection.disconnect();
```

## Bezpieczeństwo

Projekt implementuje podstawowe praktyki bezpieczeństwa:
- ✅ Hashowanie haseł przy użyciu BCrypt
- ✅ Parametryzowane zapytania SQL (PreparedStatement) zapobiegające SQL Injection
- ✅ Wysoki cost factor (12) dla BCrypt zapewniający odporność na ataki brute-force

## Autor

Projekt został stworzony w celach edukacyjnych do nauki i rozwijania umiejętności programowania w Javie.
