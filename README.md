
Quilibrium CeremonyClient node

Korzystałem z poradnika znajdującego się na tej stronie: https://demipoet.github.io/



Najpierw musimy mieć jakiś serwer VPS z odpowiednimi parametrami, szybkim łączem i bardzo dużą przepustowością. Takie są niby minimalne wymagania ale węzeł chodzi też na trochę słabszch maszynach: 

>"16GB of RAM, preferably 32 GB, 250GB of storage, preferably via SSD, 50MBps symmetric bandwidth, ie 50 download, 50 upload For Intel/AMD, the baseline processor is a Skylake processor @ 3.4GHz >with 12 dedicated cores. For ARM, the M1 line of Apple is a good reference, but this has to be a dedicated machine."

Ja korzystam z Hostingera bo parę osób ma tam uruchomionego noda i działa. Serwer jaki powinniśmy kupić to KVM 8. Najlepiej opłaca się wziąć od razu na minimum 12 miesięcy. Przy kupnie przez refa jest rabat, płacimy z góry za 12 miesięcy i za to też jest zniżka. Wychodzi 1027zł.

https://hostinger.pl?REFERRALCODE=1MASTERKILL15

Po zapłaceniu przechodzimy dalej przez wszystkie kroki, ustawiamy hasło serwera, hasło roota itd. Przy wyborze systemu z tabeli Plain OS wybieramy "Ubuntu 22.04 64bit" lub "Ubuntu 22.04 64bit with Desktop XFCE". Ja wybrałem wersję z zainstalowanym już xrdp dającym nam dostęp do pulpitu w wersji graficznej przez RDP. (with Desktop XFCE)

Jak już mamy uruchomiony serwer to trzeba się do niego jakoś zalogować. Systemy operacyjne mają możliwość logowania się przez SSH za pomocą terminalu ale najlepiej mieć do tego jakiś osobny dedykowany program do zdalnego pulpitu. Ja na linuxie używam Remmina, na windowsie wiem, że bardzo dużo osób korzysa z Putty. Ja z Putty nigdy nie korzystałem ale zasada połączenia z serwerem jest taka sama. Trzeba gdzieś wpisać adres IP, username i hasło. Typ połączenia wybrać SSH.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

Łączymy się z naszym serwerem wirtualnym przez SSH. Praktycznie wszystko robimy za pomocą wiersza poleceń. Jeżeli czegoś nie wiecie jak zrobić albo robicie to i nie wychodzi to polecam korzystanie z chataGPT albo Claude.ai Chatboty naprawdę dobrze sobie radzą i opisują działanie wszystkich komend jakie potrzebujemy. Oczywiście ja też wam we wszystkim pomogę więc śmiało pytajcie. Tak naprawdę korzystanie z terminala nie jest takie straszne na jakie wygląda ;)
 
Musimy być zalogowani jako "root". Nasz wiersz poleceń powinien wyglądać mniej więcej tak "root@srv405201:~#"

Jeżeli nie jesteśmy zalogowani jako root to po wpisaniu "sudo su" będziemy musieli wpisać nasze hasło roota.

Po wklejeniu/wpisaniu każdego polecenia klikamy ENTER. Polecenia w terminal wklejamy/wpisujemy zawsze w jednej linijce. Jeżeli edytujemy jakiś plik i coś w niego wklejamy to można w całości kilka linijek na raz.

---

Zalogowanie jako root

	sudo su
	
To polecenie przenosi nas do głównego folderu
	
	cd ~

Potem aktualizujemy nasze repozytorium

	sudo apt -q update
	
Instalujemy GITa

	sudo apt install git -y

Tym poleceniem sprawdzamy naszą wersję GIT, powinniśmy mieć "git version 2.34.1"

	git --version
	
Instalujemy GO - Golang

	wget https://go.dev/dl/go1.20.14.linux-amd64.tar.gz
	
Tym poleceniem wypakowywujemy nasz plik

	sudo tar -xvf go1.20.14.linux-amd64.tar.gz
	
Teraz przenosimy GO do innego folderu

	sudo mv go /usr/local
	
Usuwamy ten spakowany plik GO

	sudo rm go1.20.14.linux-amd64.tar.gz
	
Teraz jest trochę trudniej bo musimy edytować plik Go, użyjemy edytora Vim, tym poleceniem otwieramy nasz plik

	sudo vim ~/.bashrc
	
Żeby wejść w tryb edycji wciskamy na klawiaturze "i". Na dole powinien pojawić się napis INSERT. Scrollem albo strzałkami przewijamy na sam dół pliku, strzałkami przesuwamy kursor w prawo, tak żeby być na samym końcu lini i klikamy ENTER żeby przenieść kursor do nowej lini. Tam wklejamy te 3 linijki

    GOROOT=/usr/local/go
    GOPATH=$HOME/go
    PATH=$GOPATH/bin:$GOROOT/bin:$PATH

Jak wkleimy to wciskamy ESC, żeby wyjść z trybu edycji. Potem SHIFT + : (obok L), wpisujemy "wq" i klikamy ENTER. Zapisaliśmy i wyszliśmy z naszego pliku do terminala

Wklejamy polecenie

	source ~/.bashrc
	
I potem polecenie

	go version
	
Żeby sprawdzić wersję GO. W konsoli powinniśmy zobaczyć taki napis "go version go1.20.14 linux/amd64"


Teraz musimy przeprowadzić optymalizację ustawień sieciowych, edytujemy plik .conf i wprowadzimy parę nowych linijek. Znowu skorzystamy z edytora VIM.

	sudo vim /etc/sysctl.conf
	
Wciskamy "i", przewijamy na sam koniec, tak żeby zacząć od nowej lini. Tak samo jak robiliśmy wyżej. Wklejamy te trzy linijki

    # Increase buffer sizes for better network performance
    net.core.rmem_max=600000000
    net.core.wmem_max=600000000

Po wklejeniu ESC, SHIFT + : potem wq i ENTER żeby wyjść do konsoli

Wklejamy polecenie

	sudo sysctl  -p
	
Teraz resetujemy nasz serwer

	reboot
	
Odczekujemy chwilę, tak z 30-60 sekund i łączymy się ponownie. Jeżeli nie jesteśmy zalogowani jako "root" to ponownie wpisujemy 

	sudo su
	
I potem polecenie

	cd ~

teraz instalujemy naszego noda

	git clone https://github.com/QuilibriumNetwork/ceremonyclient.git
	
Przechodzimy do katalogu noda

	cd ceremonyclient/node
	
Teraz w katalogu noda wklejamy polecenie uruchamiające nasz węzeł

	GOEXPERIMENT=arenas go run ./...
	
Teraz czekamy kilkanaście minut aż węzeł wygeneruje klucze i zacznie pracować.
Jeżeli już widzimy w terminalu kolejne pojawiające się linijki tekstu to klikamy CTRL+C i wpisujemy

	reboot
	
Resetujemy nasz serwer, czekamy chwilę i łączymy się ponownie. Powiniśmy być jako "root"

Teraz musimy skonfigurować firewall, wklejamy polecenie

	sudo ufw enable
	
Klikamy "y" że się zgadzamy i ENTER

Teraz po kolei, po jednej linijce wklejamy polecenia i po każdym klikamy ENTER

	sudo ufw allow 22
	sudo ufw allow 8336
	sudo ufw allow 443
	
Teraz wklejamy polecenie

	sudo ufw status
	
I powinniśmy zobaczyyć takie coś w konsoli:

	> To            Action            From
	> --            ------            -----
	> 22            ALLOW             Anywhere
	> 8336          ALLOW             Anywhere
	> 443           ALLOW             Anywhere
	> 22 (v6)       ALLOW             Anywhere (v6)
	> 8336 (v6)     ALLOW             Anywhere (v6)
	> 443 (v6)      ALLOW             Anywhere (v6)
	
Teraz musimy skonfigurować plik config.yml i włączyć gRPC

Przenosimy się do katalogu noda

	cd ~/ceremonyclient/node
	
Odpalamy nasz plik edytorem VIM

	sudo vim .config/config.yml
	
Klikamy 'i', zjeżdziamy na sam koniec i tam powinna znajdować się linijka  listenGrpcMultiaddr: “”
Zmieniamy na

	listenGrpcMultiaddr: /ip4/127.0.0.1/tcp/8337
	
Mniej więcej w środkowej części pliku znajduje się napis "engine". Pod tym napisem musimy wkleić linię

	statsMultiaddr: "/dns/stats.quilibrium.com/tcp/443"
	
Zapisujemy i wychodzimy z pliku. Klikamy ESC, potem SHIFT + : i "wq" Potem ENTER

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

Teraz możemy zrobić kopię zapasową naszego klucza prywatnego i szyfrującego. Ten krok możemy pominąć i zrobić w każdej chwili. Chodzi o pliki keys.yml i config.yml znajdujące się w katalogu /ceremonyclient/node/.config
Można to zrobić na różne sposoby w zależności od tego jakim programem łączymy się z naszym serwerem i czy mamy dostęp do serwera przez desktop czy tylko przez terminal. Można je skopiować w całości albo otworzyć przez edytor VIM, skopiować zawartość i wkleić to u siebie lokalnie do pliku .txt po czym nadać mu taką samą nazwę i rozszerzenie. Ja to zrobiłem tak, że przez terminal skopiowałem te dwa pliki do innego katalogu i nadałem im pełne uprawnienia. Uruchomiłem dostęp przez desktop i przeniosłem te pliki do katalogu współdzielonego. Czyli katalogu w którym wymieniam pliki między serwerem, a moim komputerem.
Oprócz backupu kluczy musimy też spisać/skopiować nasz peer ID. Polecenie do odczytania peer ID jest w końcowej części tego pliku.

Żeby skopiować te pliki możemy wejść w odpowiedni katalog i sprawdzić jakie pliki się tam znajdują

	cd ~/ceremonyclient/node/.config/
	
Po wpisaniu "la" zobaczymy w terminalu jakie pliki są w tym folderze. Teraz utworzymy sobie nowy katalog backup za pomocą polecenia

	mkdir /home/backup

Jeżeli jesteśmy w katalogu /.config to wystarczy wpisać te dwa polecenia

	cp config.yml /home/backup/
	cp keys.yml /home/backup/

Teraz w zależności czy łączymy się przez RDP jako root, xrdpuser czy przez SFTP żeby przenieść te pliki do naszego komputera to jak nie łączymy się jako root to musimy nadać pełne uprawnienia tym plikom żeby można je było skopiować do nas lokalnie. Jeżeli łączymy się jako root przez RDP i mamy ustawiony katalog współdzielony to po prostu kopiujemy folder znajdujący się w /home/backup do naszego katalogu współdzielonego. 


Te polecenia niżej służą do nadania uprawnień dla tych plików. Jeżeli pełne uprawnienia na te konkretne pliki nie będą potrzebne to nie trzeba tego robić.
Jeżeli jesteśmy w katalogu /home/backup/ to wystarczy wpisać

	chmod 777 config.yml
	chmod 777 keys.yml
	
Jeżeli jesteśmy w innym katalogu to trzeba podać pełną ścieszkę do pliku np

	chmod 777 /home/backup/config.yml
	

	
--------------------------------------------------------------------------------------------------------------------------------------------------------------------

Teraz musimy utworzyć węzeł binarny i naszego noda jako usługę systemową. Przenosimy się do katalogu noda

	cd ~/ceremonyclient/node
	
Będąc w tym katalogu uruchamiamy polecenie

	GOEXPERIMENT=arenas go install ./...
	
Polecenie to powinno nam utworzyć katalog /root/go/bin/ Sprawdzamy to poleceniem i powinien w konsoli wyświetlić się napis "node"

	ls /root/go/bin

Teraz musimy stworzyć skrypt, który będzie odpowiadał za uruchamianie i obsługę naszego noda. Uruchamiamy polecenie

	sudo vim /lib/systemd/system/ceremonyclient.service
	
Otworzy nam się pusty plik w edytorze VIM. Z wcześniejszych kroków już wiecie jak obsługiwać VIM więc nie będę pisał kolejny raz. Trzeba wkleić ten kod i zapisać

    [Unit]
    Description=Ceremony Client Go App Service

    [Service]
    CPUQuota=720%
    Type=simple
    Restart=always
    RestartSec=5s
    WorkingDirectory=/root/ceremonyclient/node
    Environment=GOEXPERIMENT=arenas
    ExecStart=/root/go/bin/node ./...

    [Install]
    WantedBy=multi-user.target


W tym skrypcie jest ustawione maksymalne wykorzystanie rdzeni procesora na 90%. Ustawienie dotyczy 8 rdzeni. Jeżeli będziecie mieć serwer z większą ilością rdzeni to trzeba wpisać inną wartość w polu CPUQuota=. Liczy się to tak: 8 rdzeni * 90% = 720%, 12 rdzeni * 90% = 1080% itd.

Teraz tym poleceniem otwieramy okno logów naszego noda. Na razie nic tam się nie będzie pojawiało bo nasz węzeł jeszcze nie pracuje.

	sudo journalctl -u ceremonyclient.service -f --no-hostname -o cat
	
Teraz uruchamiamy nasz serwer w nowym oknie. Tamto cały czas jest otwarte. W nowo otwartym oknie, jeżeli jesteśmy rootem to wklejamy komendę uruchamiającą nasz węzeł

	service ceremonyclient start
	
Teraz jak przełączymy się na poprzednie okno to powinniśmy w logach zobaczyć jak nasz węzeł wystartował. No i to w zasadzie tyle. Węzeł nie aktualizuje się do nowszej wersji, trzeba to robić ręcznie. Jest cała sekcja o tym w dokumentacji, jak wyjdzie kolejna wersja i będę aktualizował to wszystko opisze.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------

Polecenia do obsługi noda:

Start noda

    service ceremonyclient start

Zatrzymanie noda

    service ceremonyclient stop

Status noda (CTRL+C żeby wyjść)

    service ceremonyclient status

Okno logów (CTRL+C żeby wyjść)

    sudo journalctl -u ceremonyclient.service -f --no-hostname -o cat

Sprawdzenie peer ID

    cd ~/ceremonyclient/node && GOEXPERIMENT=arenas go run ./... -peer-id

	cd ~/ceremonyclient/node && GOEXPERIMENT=arenas go run ./... --db-console
    

Sprawdzenie info o nodzie, salda itd

    cd /root/ceremonyclient/node && GOEXPERIMENT=arenas go run ./... -node-info

Sprawdzenie salda

	cd /root/ceremonyclient/node && GOEXPERIMENT=arenas go run ./... -balance

-------------------------------------------------------------------------------------------------------------------------------------------


Aktualizacja noda:

Poprzednia aktualizacja już nie działa. Jest nowy skrypt od użytkownika 0xOzgur. Wystarczy wkleić w konsolę ten link i aktualizacja zrobi się automatycznie. Ten skrypt będzie działał jak korzystamy z repozytorium na githubie.

	wget -O - https://raw.githubusercontent.com/0xOzgur/QuilibriumTools/main/update.sh | bash

Jednak wczoraj (29.05.2024) pojawił się problem i github zablokował repo Quili. Powyższa komenda może nie zadziałać dopóki nie odblokują githuba. Można się przełączyć na alternatywne repo. Wklejamy odpowiednią komendę w konsolę. Musimy być w katalogu noda żeby ta komenda zadziałała (ceremonyclient/node) 

	cd ~/ceremonyclient/node
 .

	git remote set-url origin https://source.quilibrium.com/quilibrium/ceremonyclient.git

 Teraz możemy sprawdzić czy nasze źródło się zaktualizowało

 	git remote -v

Powinniśmy zobaczyć w konsoli linki do nowego repo i zamiast githuba będzie ta strona source.quilibrium.com

 Po zmianie źródła repozytorium w konsoli wpisujemy 

 	git pull

  Po to żeby zaktualizować pliki do najnowszych wersji. Po udanej aktualizacji robimy reboot i ponownie uruchamiamy noda.

  Jeżeli wyskakują jakieś błędy to najprawdopodobniej oznacza, że nasz serwer nie widzi nowego repozytorium i trzeba zmienić serwer DNS. Można to sprawdzić komendą

   	nslookup source.quilibrium.com

Jeżeli widzimy coś w tym stylu to znaczy, że jest dobrze

	Non-authoritative answer:
	Name:	source.quilibrium.com
	Address: 172.67.73.191
	Name:	source.quilibrium.com
	Address: 104.26.7.120
	Name:	source.quilibrium.com
	Address: 104.26.6.120
	Name:	source.quilibrium.com
	Address: 2606:4700:20::681a:778
	Name:	source.quilibrium.com
	Address: 2606:4700:20::ac43:49bf
	Name:	source.quilibrium.com
	Address: 2606:4700:20::681a:678


 Jeżeli są jakieś błędy to musimy edytować plik resolv.conf

 	sudo vim /etc/resolv.conf

Na początku pliku trzeba dodać następujące linie

	nameserver 8.8.8.8  # Google DNS
	nameserver 8.8.4.4  # Google DNS

 Zapisujemy i ponownie sprawdzamy czy nasz serwer widzi nowy adres repo

 	nslookup source.quilibrium.com



Przy okazji aktualizacji i restartu serwera warto też wykonać aktualizacje systemu. Jeżeli zaktualizowaliśmy naszego noda komendą git pull i wszystko jest ok to przed rebootem możemy wpisać

	apt-get update

 Potem

 	apt-get upgrade

I teraz dopiero reboot. Nasz system na serwerze i wszystkie pliki zostaną zaktualizowane do najnowszych wersji.


























