# ortografia
import random
import math
import time

# --- STATYSTYKI GRACZA ---
miod = 0
miod_za_odpowiedz = 10  # Zwiększyłem, żeby grało się przyjemniej
poprawne_odpowiedzi = 0
zycia = 3
punkty_ujemne = 0

# --- ZWIERZAKI ---
posiadane_zwierzaki = []
ilosc_pszczolek = 0
ostatni_czas = time.time()  # Do liczenia czasu dla pszczółki

# --- BAZA PYTAŃ ---
pytania = [
    {"pytanie": "Jak napiszesz: 1. góra, czy 2. gura?", "odpowiedz": "1"},
    {"pytanie": "Jak napiszesz: 1. mroziny, czy 2. mroźny?", "odpowiedz": "2"},
    {"pytanie": "Jak napiszesz: 1. pszczułka, czy 2. pszczółka?", "odpowiedz": "2"},
    {"pytanie": "Jak napiszesz: 1. odpowiedź, czy 2. otpowiedź?", "odpowiedz": "1"},
    {"pytanie": "Jak napiszesz: 1. jeźeli, czy 2. jeżeli?", "odpowiedz": "2"}
]

def zarobek_pszczolek():
    global miod, ostatni_czas, ilosc_pszczolek
    if ilosc_pszczolek > 0:
        czas_teraz = time.time()
        minione_sekundy = int(czas_teraz - ostatni_czas)
        zarobiony_miod = minione_sekundy * (20 * ilosc_pszczolek)
        if zarobiony_miod > 0:
            miod += zarobiony_miod
            print(f"🐝 Twoje pszczółki pracowały! Zarobiły dla Ciebie +{zarobiony_miod} miodu w {minione_sekundy} sekund!")
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
        if random.random() <= 0.75: # 75% szans
            miod += 500
            print("🐱 Kot przyniósł Ci szczęście! Dostajesz +500 miodu!")
        else:
            print("🐱 Kot poszedł spać. Tym razem nie ma miodu.")
    elif wylosowany == "Pancernik":
        print("🦔 Pancernik dołącza do drużyny! Od teraz chroni Cię przed 50% punktów ujemnych!")
    elif wylosowany == "Pszczółka":
        ilosc_pszczolek += 1
        print("🐝 Pszczółka dołącza do ula! Będzie produkować 20 miodu na sekundę!")

def losowanie_kart():
    global miod, miod_za_odpowiedz
    print("\n--- 🎴 ZDOBYWASZ 3 KARTY! 🎴 ---")
    input("Wciśnij Enter, aby odkryć kartę...")
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
print("Odpowiadaj poprawnie, zbieraj miód, unikaj utraty żyć i zdobywaj zwierzaki!")

while zycia > 0:
    zarobek_pszczolek() # Oblicza miód z pszczół na początku tury
    
    print("\n" + "="*40)
    print(f"🍯 Miód: {miod} | ❤️ Życia: {zycia} | 💀 Punkty ujemne: {punkty_ujemne}")
    if posiadane_zwierzaki:
        print(f"🐾 Twoje zwierzaki: {', '.join(posiadane_zwierzaki)}")
    print("="*40)

    # --- LOSOWANIE EVENTÓW ---
    event = random.choice(["Brak", "Mroźny", "Wodny", "Mroczny", "Złowrogi"])
    mnoznik_miodu = 1
    kara_za_blad = 1  # 1 życie
    kary_ujemne = 10  # 10 punktów ujemnych za błąd domyślnie
    
    if event == "Mroźny":
        print("❄️ EVENT MROŹNY! Jeśli się pomylisz, dostaniesz mniej punktów ujemnych!")
        kary_ujemne = 5
    elif event == "Wodny":
        print("🌊 EVENT WODNY! Poprawna odpowiedź daje x2 Miodu!")
        mnoznik_miodu = 2
    elif event == "Mroczny":
        utracony = math.floor(miod * 0.25)
        miod -= utracony
        zycia += 1
        print(f"🌑 EVENT MROCZNY! Koszmar zabiera 25% miodu (-{utracony}), ale zyskujesz +1 Życie!")
    elif event == "Złowrogi":
        print("👿 EVENT ZŁOWROGI! Uważaj! Zła odpowiedź zabiera aż 2 ŻYCIA!")
        kara_za_blad = 2
        
    # --- ZŁOTA ODPOWIEDŹ ---
    zlota_szansa = random.random() <= 0.10  # 10% szans
    if zlota_szansa:
        print("🌟 ZŁOTA ODPOWIEDŹ! Odpowiedz poprawnie, aby zdobyć zwierzaka!")

    # --- ZADAWANIE PYTANIA ---
    pytanie = random.choice(pytania)
    print("\n" + pytanie["pytanie"])
    odp = input("Wybierz 1 lub 2: ")
    
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
        
        # Pancernik zmniejsza punkty ujemne
        if "Pancernik" in posiadane_zwierzaki:
            kary_ujemne = math.floor(kary_ujemne / 2)
            print("🦔 Twój pancernik blokuje połowę punktów ujemnych!")
            
        punkty_ujemne += kary_ujemne
        print(f"❌ Źle! Tracisz {kara_za_blad} życie i dostajesz +{kary_ujemne} punktów ujemnych.")

    time.sleep(1) # Krótka pauza żeby tekst nie mignął za szybko

print("\n" + "💀 "*10)
print(f"KONIEC GRY! Straciłeś wszystkie życia.")
print(f"Twój wynik:")
print(f"🍯 Zebrany miód: {miod}")
print(f"💀 Punkty ujemne: {punkty_ujemne}")
print(f"🐾 Zdobyte zwierzaki: {len(posiadane_zwierzaki)}")
