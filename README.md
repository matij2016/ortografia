import random
import math
import time

# --- STATYSTYKI GRACZA ---
miod = 0
miod_za_odpowiedz = 10
poprawne_odpowiedzi = 0
zadane_pytania = 0  # Nowy licznik do zmiany etapów!
zycia = 3
punkty_ujemne = 0

# --- ZWIERZAKI ---
posiadane_zwierzaki = []
ilosc_pszczolek = 0
ostatni_czas = time.time()

# --- BAZA PYTAŃ PODZIELONA NA KATEGORIE ---
baza_pytan = {
    "Ó czy U": [
        {"pytanie": "Jak napiszesz: 1. góra, czy 2. gura?", "odpowiedz": "1"},
        {"pytanie": "Jak napiszesz: 1. pszczułka, czy 2. pszczółka?", "odpowiedz": "2"},
        {"pytanie": "Jak napiszesz: 1. ulu, czy 2. óló?", "odpowiedz": "1"},
        {"pytanie": "Jak napiszesz: 1. głuwna, czy 2. główna?", "odpowiedz": "2"}
    ],
    "RZ czy Ż": [
        {"pytanie": "Jak napiszesz: 1. rzeka, czy 2. żeka?", "odpowiedz": "1"},
        {"pytanie": "Jak napiszesz: 1. rzaba, czy 2. żaba?", "odpowiedz": "2"},
        {"pytanie": "Jak napiszesz: 1. zwierzę, czy 2. zwieżę?", "odpowiedz": "1"},
        {"pytanie": "Jak napiszesz: 1. jeźeli, czy 2. jeżeli?", "odpowiedz": "2"}
    ],
    "CH czy H": [
        {"pytanie": "Jak napiszesz: 1. chmura, czy 2. hmura?", "odpowiedz": "1"},
        {"pytanie": "Jak napiszesz: 1. herbata, czy 2. cherbata?", "odpowiedz": "1"},
        {"pytanie": "Jak napiszesz: 1. bohater, czy 2. bochater?", "odpowiedz": "1"},
        {"pytanie": "Jak napiszesz: 1. chleb, czy 2. hleb?", "odpowiedz": "1"}
    ],
    "Miękkie: Ś, Ź, Ć, Ń": [
        {"pytanie": "Jak napiszesz: 1. mroziny, czy 2. mroźny?", "odpowiedz": "2"},
        {"pytanie": "Jak napiszesz: 1. odpowiedź, czy 2. otpowiedź?", "odpowiedz": "1"},
        {"pytanie": "Jak napiszesz: 1. słońce, czy 2. słonice?", "odpowiedz": "1"},
        {"pytanie": "Jak napiszesz: 1. ćma, czy 2. ciema?", "odpowiedz": "1"}
    ]
}

kategorie = list(baza_pytan.keys())
poprzednia_kategoria = ""

def zarobek_pszczolek():
    global miod, ostatni_czas, ilosc_pszczolek
    if ilosc_pszczolek > 0:
        czas_teraz = time.time()
        minione_sekundy = int(czas_teraz - ostatni_czas)
        zarobiony_miod = minione_sekundy * (20 * ilosc_pszczolek)
        if zarobiony_miod > 0:
            miod += zarobiony_miod
            print(f"🐝 Twoje pszczółki zarobiły dla Ciebie +{zarobiony_miod} miodu w {minione_sekundy} sekund!")
        ostatni_czas = czas_teraz
    else:
        ostatni_czas = time.time()

def losuj_zwierzatko():
    global zycia, miod, ilosc_pszczolek
    zwierzaki = ["Pies", "Kot", "Pancernik", "Pszczółka"]
    wylosowany = random.choice(zwierzaki)
    posiadane_zwierzaki.append(wylosowany)
    
    print(f"\n✨ ZŁOTA ODPOWIEDŹ! Otrzymujesz zwierzaka: {wylosowany.upper()}! ✨")
    
    if wylosowany == "Pies":
        zycia += 2
        print("🐶 Pies dodaje Ci +2 życia!")
    elif wylosowany == "Kot":
        if random.random() <= 0.75:
            miod += 500
            print("🐱 Kot przyniósł Ci szczęście! Dostajesz +500 miodu!")
        else:
            print("🐱 Kot poszedł spać. Tym razem nie ma miodu.")
    elif wylosowany == "Pancernik":
        print("🦔 Pancernik dołącza do drużyny! Chroni przed 50% punktów ujemnych!")
    elif wylosowany == "Pszczółka":
        ilosc_pszczolek += 1
        print("🐝 Pszczółka dołącza do ula! Produkuje 20 miodu na sekundę!")

def losowanie_kart():
    global miod, miod_za_odpowiedz
    print("\n--- 🎴 ZDOBYWASZ KARTĘ! 🎴 ---")
    typ_karty = random.randint(1, 4)
    
    if typ_karty == 1:
        ile = random.randint(10, 1000)
        miod += ile
        print(f"KARTA: Znalazłeś ukryty miód! +{ile}")
    elif typ_karty == 2:
        strata = random.choice([0.25, 0.50, 0.75])
        utracony = math.floor(miod * strata)
        miod -= utracony
        print(f"KARTA: Dziura w ulu! Tracisz {int(strata*100)}% miodu (-{utracony}).")
    elif typ_karty == 3:
        print("KARTA: Pusta karta. Nic się nie dzieje.")
    elif typ_karty == 4:
        ile_wiecej = random.randint(5, 50)
        miod_za_odpowiedz += ile_wiecej
        print(f"KARTA: Uczysz się! Za dobrą odpowiedź dostajesz o +{ile_wiecej} miodu więcej!")

# --- GŁÓWNA PĘTLA GRY ---
print("WITAJ W EPICKIEJ GRZE ORTOGRAFICZNEJ!")
print("Zasady zmieniają się co 10 pytań. Bądź czujny!")

while zycia > 0:
    zarobek_pszczolek()
    
    # --- ZMIANA KATEGORII CO 10 PYTAŃ ---
    indeks_kategorii = (zadane_pytania // 10) % len(kategorie)
    aktualna_kategoria = kategorie[indeks_kategorii]
    
    if aktualna_kategoria != poprzednia_kategoria:
        print("\n" + "🌟"*20)
        print(f"🏆 NOWY ETAP! Twoja główna zasada to teraz: {aktualna_kategoria.upper()}! 🏆")
        print("🌟"*20)
        poprzednia_kategoria = aktualna_kategoria

    print("\n" + "="*40)
    print(f"🍯 Miód: {miod} | ❤️ Życia: {zycia} | 💀 Ujemne pkt: {punkty_ujemne} | ❓ Pytanie: {zadane_pytania + 1}")
    if posiadane_zwierzaki:
        print(f"🐾 Zwierzaki: {', '.join(posiadane_zwierzaki)}")
    print("="*40)

    # --- LOSOWANIE EVENTÓW ---
    event = random.choice(["Brak", "Mroźny", "Wodny", "Mroczny", "Złowrogi"])
    mnoznik_miodu = 1
    kara_za_blad = 1
    kary_ujemne = 10
    
    if event == "Mroźny":
        print("❄️ EVENT MROŹNY! Mniej punktów ujemnych za błąd!")
        kary_ujemne = 5
    elif event == "Wodny":
        print("🌊 EVENT WODNY! Poprawna odpowiedź daje x2 Miodu!")
        mnoznik_miodu = 2
    elif event == "Mroczny":
        utracony = math.floor(miod * 0.25)
        miod -= utracony
        zycia += 1
        print(f"🌑 EVENT MROCZNY! Tracisz 25% miodu, ale zyskujesz +1 Życie!")
    elif event == "Złowrogi":
        print("👿 EVENT ZŁOWROGI! Zła odpowiedź zabiera aż 2 ŻYCIA!")
        kara_za_blad = 2
        
    zlota_szansa = random.random() <= 0.10
    if zlota_szansa:
        print("🌟 ZŁOTA ODPOWIEDŹ! Odpowiedz poprawnie, aby zdobyć zwierzaka!")

    # --- ZADAWANIE PYTANIA Z AKTUALNEJ KATEGORII ---
    pytanie = random.choice(baza_pytan[aktualna_kategoria])
    print("\n" + pytanie["pytanie"])
    odp = input("Wybierz 1 lub 2: ")
    
    zadane_pytania += 1 # Dodajemy pytanie do licznika
    
    # --- SPRAWDZANIE ODPOWIEDZI ---
    if odp == pytanie["odpowiedz"]:
        zdobyty = miod_za_odpowiedz * mnoznik_miodu
        miod += zdobyty
        poprawne_odpowiedzi += 1
        print(f"✅ Dobrze! Dostajesz +{zdobyty} miodu.")
        
        if zlota_szansa:
            losuj_zwierzatko()
            
        if poprawne_odpowiedzi % 3 == 0:
            losowanie_kart()
            
    else:
        zycia -= kara_za_blad
        if "Pancernik" in posiadane_zwierzaki:
            kary_ujemne = math.floor(kary_ujemne / 2)
            print("🦔 Twój pancernik blokuje połowę punktów ujemnych!")
            
        punkty_ujemne += kary_ujemne
        print(f"❌ Źle! Tracisz {kara_za_blad} życie i dostajesz +{kary_ujemne} punktów ujemnych.")

    time.sleep(1)

print("\n" + "💀 "*10)
print(f"KONIEC GRY! Przetrwałeś {zadane_pytania} pytań.")
print(f"Twój wynik:")
print(f"🍯 Zebrany miód: {miod}")
print(f"💀 Punkty ujemne: {punkty_ujemne}")
print(f"🐾 Zdobyte zwierzaki: {len(posiadane_zwierzaki)}")
