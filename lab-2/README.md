# Laboratorium numer 2

Docker - budowa i uruchamianie pojedynczych kontenerów
Docker-compose - uruchamianie stosu

AWS App Runner - wdrożenie środowiska z wykorzystaniem gotowego narzędzia (podobne do portalu Render z poprzednich zajęć)
AWS ECS - Elastic Container Service - wdrażanie aplikacji na klaster podobny do docker-service

AWS - EC2 - tworzenie maszyn wirtualnych terraform

## Przed laboratorium - utworzenie środowiska deweloperskiego

Z uwagi na to, iz ćwiczenia są realizowane w trybie zdalnym proponuje dwa rozwiązania odnośnie pierwszej części ćwiczenia:

- [PlayWithDocker](https://labs.play-with-docker.com/)
- własne środowisko lokalne: System dowolny, lecz wymagana instalacja:
  - [Docker](https://docs.docker.com/engine/install/)
  - [Docker-compose](https://docs.docker.com/compose/install/) (tylko w przypadku systemów bazujących na Linuksie)

Druga część laboratorium wymaga aktywnego konta AWS i w tym celu należy wykonać instrukcje:

- wchodzimy na stronę: [AWS Free Tier](https://aws.amazon.com/free)
- zapoznajemy się z [limitami](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all) (uwaga: przekroczenie tych limitów skutkuje obciążeniem karty, która podamy podczas rejestracji!)

- zakładamy konto (albo używamy tego które juz mamy)
- Sugestia zabezpieczeń: ustawiamy hasło z generatora np naszego managera haseł
- podajemy dane teleadresowe (poprawne i prawdziwe)

- podajemy kartę - polecam tutaj użycie Revolut Virtual Card (np. z limitem np. 10zł)
  jeśli nie posiadamy Revolut'a to podajemy kartę debetową z naszego banku 
- po utworzeniu i zweryfikowaniu konta (opłata 1$) logujemy się ponownie do konsoli głównego konta `root`

[Instrukcja zerowych kosztów w AWSie](<https://aws.amazon.com/getting-started/hands-on/control-your-costs-free-tier-budgets/>
Rozpoczynamy od kroku 3
Szybkie podsumowanie kroków:

- wchodzimy do AWS Billing Dashboard
- po lewej stronie strony wyszukujemy "Budgets" i klikamy w "Create a Budget"
- tworzymy zasugerowane rozwiązanie "Zero spend budget" i w "Email recipients" podajemy nasz adres email, na który zostaniemy poinformowani, ze zbliżamy sie do limitu kosztów w ramach bezpłatnego planu

Instrukcja dodatkowych zabezpieczeń (przed wyłudzeniem klucza do konta):
<https://docs.aws.amazon.com/accounts/latest/reference/best-practices-root-user.html>

Podsumowanie dokumentu:

- [MFA](https://docs.aws.amazon.com/accounts/latest/reference/root-user-mfa.html) (dwuskładnikowe uwierzytelnienie) dla konta głównego `root` (konto z adresem e-mail)
- Konto Root'a jest kontem tylko do zarządzania, ale nie tworzenia infrastruktury!
- Osobne konto do tworzenia zasobów z możliwym logowaniem do CLI, API, Konsoli Web
[Opis jak stworzyć takiego użytkownika](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html?icmpid=docs_iam_console#id_users_create_console)
- Jakie uprawnienia nadać użytkownikowi - ja sugeruje Administratora: `AdministratorAccess policy`
Do przeczytania o tym jak to zrobić (tworzenie grupy jest opcjonalne): [Admin user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
- Po stworzeniu ukaże sie unikalny adres, na który można zalogować się bezpośrednio bez loginu głównego konta `root`, czyli tzw. adres organizacji: np. <https://123123123.signin.aws.amazon.com/console>
- zapisujemy/wysyłamy email sobie z ustawieniami wstępnymi użytkownika
- następnie logujemy sie przez stworzony dla nas adres organizacji wykorzystując stworzonego użytkownika
- zmieniamy hasło (i tu znowu sugeruje użycie generatora haseł oraz dodanie uwierzytelnienia dwuskładnikowego)

## Zadanie 1 - Docker

Uruchamianie usługi w kontekście lokalnym na skonteneryzowanym środowisku
Zadanie ma na celu przypomnienie jak należy używać Dockera

- Wykonaj fork repozytorium `iac-labs` na portalu GitHub
- Wykonaj klon repozytorium do systemu operacyjnego, w kontekście którego wykonujesz laboratorium
- Przejdź do katalogu `example-app`
- Otwórz plik `Dockerfile` i zapoznaj się z nim - jeśli coś jest w tym pliku niezrozumiałe będziemy tłumaczyć
- Wybuduj obraz z wykorzystaniem polecenia `docker build` w celu utworzenia obrazu
(do polecenia dodaj `--tag` i numerem swojego indeksu)
- Uruchom wybudowany obraz poleceniem `docker run`
- Sprawdź działanie uruchomionej aplikacji wykonując polecenie `curl http://localhost:8000` lub wchodząc pod adres z przeglądarki na swoim systemie operacyjnym
- Dodaj do powyższego polecenia dodatkowe parametry uruchomienia: `publish` - otwórz port: `8000:8000`, przekaz również plik ustawień zmiennych środowiskowych: `--env-file` nazwany `env.docker` oraz przydziel nazwę kontenerowi np `--name app`
- Uruchom dodatkowo serwer bazy danych, który nie jest opisany przez `Dockerfile` lecz z uwagi na to, iz istnieje możliwość lokalnego uruchamiania kontenerów z rejestru <https://hub.docker.com> to pobierz obraz bazy danych `postgres` (poleceniem `docker pull`)
  Następnie wykonaj polecenie `docker run --name db --env-file env.docker postgres` w celu uruchomienia
- Zweryfikuj działanie aplikacji - jeśli aplikacja nie mozę dostać sie do bazy danych to dwa poprzednie polecenia należy zmodyfikować o dodatkową siec współdzieloną `docker network create lab2zad1` i podpiąć do kontenerów które juz działają poleceniem:
  `docker network connect lab2zad1 (nazwa kontenera)`
- Zweryfikuj ponownie działanie
- Zatrzymaj kontenery - poleceniem `docker stop`
- Usuń kontenery - poleceniem `docker rm`

Pytania do zadania:

- Czym jest obraz kontenera?
- Jak działają warstwy obrazu kontenera?
- Czym różni sie kontener od obrazu?
- Dlaczego musieliśmy dodatkowo dodać siec, by komunikować dwa kontenery ze soba, skoro działają one na jednym systemie operacyjnym?
- Wymień elementy konfigurujące środowisko uruchomieniowe (_runtime environment_) w pliku `Dockerfile`

## Zadanie 2 - Docker-compose

Uruchamianie stosu aplikacji w ramach jednej maszyny

- Otwórz plik `docker-compose.yaml` i zapoznaj się z nim - jeśli coś jest w tym pliku niezrozumiałe będziemy tłumaczyć
- Uruchom stos poleceniem `docker-compose up`
- Zmodyfikuj działanie klastra dodając brakująca siec (blok `networks` i dowiązanie do kontenerów)

Pytania do zadania:

- Czym jest `docker-compose` w stosunku do poleceń Dockera z poprzedniego zadania?
- Jak nazywane są kontenery działające w ramach stosu?
- Czym jest stos aplikacji?

## Zadanie 3 - wykorzystanie AWS App Runner w celu tworzenia aplikacji w chmurze

Zadanie ma na celu pokazać podobne rozwiązanie do portalu Render z poprzedniego laboratorium, lecz w kontekście chmury IaaS - AWS

- Wykorzystując zmodyfikowany przez siebie kod aplikacji wykonaj polecenie `git add .`, `git commit` i `git push` na swoim forku, tak by zmiany wykonane w poprzednich ćwiczeniach znalazły się na głównej gałęzi `main`

- Zaloguj się na swoje konto użytkownika AWSowe
- Wyszukaj na górze strony serwis AWS App Runner
- 