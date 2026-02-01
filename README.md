# Projekt: Mikroserwis do analizy plików FASTQ (FastQC + NGINX)
**Słuchacz:** PD5036

## Opis projektu
Projekt realizuje mikroserwis oparty na kontenerach Docker, który automatyzuje analizę jakości surowych danych sekwencyjnych i udostępnia wyniki poprzez serwer www.

System składa się z dwóch kontenerów:
1. **fastqc_container**: Obraz zbudowany od podstaw (Ubuntu 22.04 + FastQC), który analizuje pliki FASTQ.
2. **nginx_container**: Serwer WWW hostujący i wyświetlający wygenerowane raporty HTML.

### Konfiguracja plików projektowych
* **Dockerfile**: Definiuje środowisko FastQC (Ubuntu 22.04, Java 11, instalacja narzędzia).
* **nginx.conf**: Konfiguruje serwer NGINX tak, aby automatycznie wyświetlał wygenerowany raport HTML jako stronę główną.
* **docker-compose.yml**: Łączy oba kontenery, definiuje sieć `fastqc_network` oraz wolumen `fastqc_results`.

###kroki konfiguracji i automatyzacja procesu

Podczas uruchamiania projektu za pomocą Docker Compose, system wykonuje automatycznie następujące etapy konfiguracji:

1. **Budowa obrazu FastQC**: 
   Na podstawie pliku `Dockerfile` system buduje obraz bazujący na Ubuntu 22.04. Instalowane są zależności systemowe (Java 11, Perl, Unzip, Wget) oraz binarne pliki narzędzia **FastQC v0.12.1**.

2. **Utworzenie wolumenu i sieci**:
   Docker tworzy trwały wolumen o nazwie `fastqc_results`. Jest on montowany w dwóch miejscach:
   * W kontenerze FastQC do zapisu wyników (`/results`).
   * W kontenerze NGINX do odczytu danych (`/usr/share/nginx/html`).
   Jednocześnie tworzona jest izolowana sieć `fastqc_network` dla bezpiecznej komunikacji między usługami.

3. **Analiza danych (Pipeline)**:
   Kontener `fastqc_container` montuje lokalny folder `input` w trybie tylko do odczytu (`:ro`). Pobiera stamtąd plik sekwencyjny, przetwarza go i generuje raport HTML wewnątrz wolumenu.

4. **Udostępnienie raportu (NGINX)**:
   Kontener `nginx_container` wczytuje plik konfiguracyjny `nginx.conf`, który definiuje ścieżkę do plików oraz stronę główną. Serwer wystawia wyniki na porcie **8080** hosta.




