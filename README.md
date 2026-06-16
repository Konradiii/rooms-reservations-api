# Rooms & Reservations API

REST API do zarządzania salami dydaktycznymi i ich rezerwacjami, zbudowane w **ASP.NET Core Web API** (C#) w architekturze opartej na kontrolerach. Projekt skupia się na fundamentach REST API: routingu, metodach HTTP, model bindingu oraz poprawnych kodach statusu.

## Opis

Aplikacja stanowi backend dla centrum szkoleniowego zarządzającego salami i rezerwacjami warsztatów. Dane przechowywane są **w pamięci aplikacji** (statyczne listy inicjalizowane przy starcie) — projekt celowo nie korzysta z bazy danych, aby skupić się na poprawnym zaprojektowaniu warstwy API i obsłudze żądań HTTP.

## Modele danych

**Room** — `Id`, `Name`, `BuildingCode`, `Floor`, `Capacity`, `HasProjector`, `IsActive`

**Reservation** — `Id`, `RoomId`, `OrganizerName`, `Topic`, `Date`, `StartTime`, `EndTime`, `Status` (planned / confirmed / cancelled)

## Endpointy

### Sale (`RoomsController`)
| Metoda | Ścieżka | Opis |
|--------|---------|------|
| `GET` | `/api/rooms` | Wszystkie sale |
| `GET` | `/api/rooms/{id}` | Sala po identyfikatorze |
| `GET` | `/api/rooms/building/{buildingCode}` | Sale z danego budynku (parametr z trasy) |
| `GET` | `/api/rooms?minCapacity=20&hasProjector=true&activeOnly=true` | Filtrowanie po query stringu |
| `POST` | `/api/rooms` | Dodanie sali |
| `PUT` | `/api/rooms/{id}` | Pełna aktualizacja sali |
| `DELETE` | `/api/rooms/{id}` | Usunięcie sali |

### Rezerwacje (`ReservationsController`)
| Metoda | Ścieżka | Opis |
|--------|---------|------|
| `GET` | `/api/reservations` | Wszystkie rezerwacje |
| `GET` | `/api/reservations/{id}` | Rezerwacja po identyfikatorze |
| `GET` | `/api/reservations?date=...&status=...&roomId=...` | Filtrowanie po query stringu |
| `POST` | `/api/reservations` | Utworzenie rezerwacji |
| `PUT` | `/api/reservations/{id}` | Aktualizacja rezerwacji |
| `DELETE` | `/api/reservations/{id}` | Usunięcie rezerwacji |

Projekt ćwiczy trzy sposoby przekazywania danych: parametry z **trasy** (`id`, `buildingCode`), filtry z **query stringa** oraz obiekty z **body** żądania (JSON).

## Walidacja i reguły biznesowe

- **Walidacja** (Data Annotations): wymagane pola (`Name`, `BuildingCode`, `OrganizerName`, `Topic`), `Capacity` większe od zera, `EndTime` późniejszy niż `StartTime`. Błędne dane → `400 Bad Request`.
- Nie można zarezerwować sali, która nie istnieje lub jest nieaktywna.
- Dwie rezerwacje tej samej sali nie mogą nakładać się czasowo tego samego dnia (kolizja → `409 Conflict`).

## Kody statusu HTTP

`200 OK` (odczyt/aktualizacja), `201 Created` (utworzenie, z `CreatedAtAction`), `204 No Content` (usunięcie), `400 Bad Request` (błędne dane), `404 Not Found` (brak zasobu), `409 Conflict` (kolizja rezerwacji).

## Technologie

- C# / .NET — ASP.NET Core Web API (kontrolery)
- Data Annotations (walidacja)
- Przechowywanie danych in-memory (statyczne listy)

## Uruchomienie

1. Uruchom aplikację (`dotnet run`) — dane startowe (kilka sal i rezerwacji) inicjalizują się automatycznie.
2. Testuj endpointy w Postmanie lub Swaggerze, sprawdzając zarówno poprawne, jak i błędne przypadki (walidacja, kody statusu).
