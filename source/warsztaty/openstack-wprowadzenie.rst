Openstack - wprowadzenie
========================

Prezentacja
-----------

* Prezentacja wprowadzająca: :download:`[PDF]<../_static/openstack_wprowadzenie.pdf>`
* Wydarzenie `FB <https://www.facebook.com/events/553461315198184/>`_

Ćwiczenia - DevStack
--------------------

**Uwaga: instaluj DevStacka na maszynie wirtualnej np. VirtualBox lub KVM**

Najłatwiej zacząć z dystrybucją OpenStacka dla programistów: DevStack. Aby zainstalować DevStacka postępuj według `instrukcji Quick Start <https://docs.openstack.org/devstack/latest/>`_

Po instalacji możesz odwiedzić dashboard OpenStacka pod adresem:

::

    http://<ADRES_VM>/dashboard/

Zaloguj się na konto *admin* wybranym przy instalacji hasłem, domyślnie *secret*.

Tworzymy prywatną sieć wirtualną
--------------------------------

Zacznijmy od zobaczenia topologii sieci. Po zalogowaniu się w dashboardzie przejdź do zakładki:

::

    Project -> Network -> Network Topology

Powinniście ujrzeć sieć o nazwie *Public*. Jest to sieć provider typu external, została stworzona przy instalacji OpenStacka i zapewnia dostęp 'na zewnątrz'.

Zależnie od konfiguracji OpenStacka, możemy podłączyć instancję (VM lub kontener) do sieci provider(jeśli nie jest to *external*) lub sieci prywatnej. W naszym wypadku wybierzemu drugą opcję, aby stworzyć nową sieć prywatną postępuj według poniższych instrukcji:

- Będąc w zakładce topologii, wybierz *Create Network*
- Wprowadź nazwę sieci i wybierz *Next*
- Wprowadź nazwę podsieci, adres np. 192.168.100.0/24, adres bramy głownej 192.168.100.1 i wybierz *Next*
- Wprowadź zakres adresów do alokacji przez DHCP, np. '192.168.100.100,192.168.100.200' i wybierz *Create*

W widoku topologii powstanie nowa sieć o wybranej przez Ciebie nazwie. Chcemy mieć dostęp do internetu, więc połączymy ją do sieci *Public* poprzez *router*.

- Wybierz *Create router*
- Wejdź do zakładki *Routers*
- Wybierz akcję *Set Gateway* dla nowego routera, z listy opcji wybierz sieć *Public* i zaakceptuj
- Następnie dalej w widoku *Routers* wybierz swój router klikając na jego nazwę
- W zakładce interfaces wybierz *Add interface* i z listy sieci wybierz swoją sieć prywatną utworzoną wcześniej, zaakceptuj

W widoku topologii powinny pojawić się połączenia pomiędzy routerem a sieciami *Public* i twoją siecią prywatną. Następnie zajmiemy się stworzeniem nowego adresu pływającego, który przydzielimy naszej instancji, by była dostępna poprzez NAT z zewnątrz.

- Wybierz zakładkę *Floating IPs* znajdującą się w pod zakładką *Network*
- Wybierz *Allocate IP To Project* i z listy puli wybierz tą należącą do sieci *Public*, zaakceptuj *Allocate IP*

Ostatnie co nam zostało to ustawić polityki sieci, by firewall wpuszczał ruch ICMP i SSH do naszej instancji. W tym celu stworzymy nową grupę bezpieczeństwa.

- Wybierz zakładkę *Security Groups*, a następnie przycisk *Create Security Group*
- Wprowadź nazwę i zaakceptuj, następnie wybierz akcję *Manage Rules* na nowej grupie
- Wybierz *Add Rule*, z listy *Rule* wybierz *All ICMP* i kierunek *ingress*, resztę parametrów pozostaw bez zmian i dodaj regułę *Add*
- Następnie znów wybierz *Add Rule* tym razem z listy reguł wybierając *SSH* i dodaj regułę

Tworzymy pierwszą instancję
---------------------------

Możemy teraz przystąpić do tworzenia instancji. Przejdź do widoku instancji.

::

    Project -> Compute -> Instances

Aby stworzyć instancję postępuj według kolejnych kroków.

- W widoku instancji wybierz *Create instance*
- Wprowadź nazwę instancji i wybierz *Next*
- Z listy dostępnych obrazów wybierz *Cirros* strzałką skierowaną w górę, następnie wybierz *Next*
- Z listy dostępnych *flavor* wybierz najmniejszą możliwą konfigurację, wybierz *Next*
- W zakładce *Networks* dodaj utworzoną wcześniej *sieć prywatną*
- Pomiń zakładkę *Network Ports*, porty zostaną stworzone automatycznie
- W zakładce *Security Groups* dodaj utworzoną wcześniej grupę pozwalającą na dostęp ICMP i SSH
- Wybierz *Launch instance*, nie potrzebujemy reszty konfiguracji

Po chwili nasza instacja powinna przyjąć stan *Running*. Możemy teraz powiązać jej interfejs z utworzonym wcześniej *Floating IP*.

- Z listy akcji dla twojej instancji wybierz *Associate Floating IP* i powiąż z jedynym istniejącym interfejsem na naszej sieci prywatnej

Z twojej maszyny wirtualnej, na której zainstalowany jest DevStack, powinien być osiągalny pływający adres IP twojej nowej instancji.

::

    ping 172.X.X.X
    ssh cirros@172.X.X.X
    # następnie podaj stałe hasło dla CirrOS: 'gocubsgo' lub 'cubswin:)'

Do swojej instancji możesz się również dostać za pomocą VNC, wybierając jej nazwę na liście dostępnych instancji a następnie zakładkę *Console*.

Dodatkowe instrukcje i pojęcia
------------------------------

Przy pomocy *Flavors* możesz określać konfiguracje zasobów, które można przyznać instancji. Nowy *flavor* możesz dodać z uprawnieniami administratora.

::

    Admin -> Compute -> Flavors

Nowe obrazy instancji możesz dodać poprzez:

::

    Project -> Compute -> Images

Jeśli chcesz dodać lub utworzyć klucz do logowania się na instancji przez SSH odwiedź zakładkę *Key Pairs* oraz przy *tworzeniu instancji* dodaj swój klucz z listy.

::

    Project -> Compute -> Key Pairs

Więcej informacji odnajdziesz w `dokumentacji OpenStacka <https://docs.openstack.org>`_
