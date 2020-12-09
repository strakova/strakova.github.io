# Digital Voicing of Silent Speech

David Gaddy a Dan Klein

EMNLP 2020 Best Paper Award

[Původní článek](https://www.aclweb.org/anthology/2020.emnlp-main.445/)

## Krátká verze

Digital Voicing of Silent Speech (https://arxiv.org/abs/2010.02960): dělali ozvučení nahrávky řeči podle signálů EMG (elektrody na obličeji), ale jenom tiché řeči, tj. artikulované. Protože v tom případě nemáš žádnou nahrávku, tj. target, tak výsledky upadají, když to dáš rozpoznat modelu trénovaném v normálním nastavení (EMG na vstupu, skutečné audio na výstupu), protože při tiché artikulaci jsou ty EMG signály přece jen jiné. Tak dělali transfer learning: vyrobili nový korpus, ve kterém nahráli EMG signály téhož textu dvakrát, jednou s audiem a jednou jenom artikulaci, pak na sebe ty EMG signály napasovali a získali upravený target. Protože to nikdo ještě neudělal (silent speech je trochu díra na trhu, plus k tomu ta data doteď nebyla), tak ty výsledky ohromně poskočily: na *closed vocabulary* (tj. malý, omezený slovníček) snížili absolutní chybovost na slovech z 64% na 4% (!) a na *open vocabulary* (libovolný text, celý slovník jazyka) snížili relativní chybu mezi 88% a 68%.

## Dlouhá verze

**Digital voicing** je proces ozvučování němého záznamu mluvčího. Speciálnější variantou je **silent speech**, kdy mluvčí něco říkal beze zvuku, jen artikuloval. Zatímco v prvním případě máme tedy pro učení k dispozici skutečnou nahrávku toho, co bylo řečeno, v druhém případě nahrávka chybí, respektive je prázdná.

K čemu je to dobré vůbec umět – pro ozvučování v situacích, kdy mluvčí nechce rušit své okolí, nebo je okolí tak rušné, že není možné promluvu vůbec zachytit, případně pro pacienty, kteří mluvit vůbec nemůžou.

Základní vstupní informací je elektromagnetický záznam elektrod umístěných na obličeji. **EMG**, electromyography, je zařízení na měření elektromagnetických signálů ze svalů. Máme tedy k dispozici EMG signál z obličejových svalů, ale žádné cílová data (**target**) audio data pro učení.

V normálních případech obyčejného digital voicing se použije obvyklá mašinérie strojového učení, protože máme k dispozici vstup (EMG signál) a výstup (audio). Jakmile naučíme náš model převádět EMG signál na nějaké klíčové vlastnosti audia, můžeme tyto vlastnosti vložit do hlasového syntetizátoru (jeden takový známý se jmenuje WaveNet) a máme vyhráno.

V případe „silent speech“ ale nemáme na co převádět, nemáme takzvaný „target“. Samozřejmě se můžeme pokusit využít existující, již natrénované modely pro normální hlasitou řeč, můžeme očekávat, že ty modely to nějak zvládnou, i když vstup je poněkud jiný. Při pouhé artikulaci mluvíme trochu jinak, ale to nevadí, pojedeme přes mrtvoly a stejně ten vstup z tiché řeči nandáme do sítě, která umí ozvučit EMG signály nahrané při hlasité řeči. A ono to skutečně bude trochu fungovat, ale výkon bude nižší a horší, protože ta síť byla zvyklá na něco trochu jiného.

Nejsme na tom ale zase tak špatně, protože v takové situaci se dá použít proces zvaný **transfer learning**, což je schopnost převést znalost z již předtrénovaného, alespoň trochu v něčem podobného modelu do nových podmínek. 

V tomto případě autoři vytvořili nový korpus, ve kterém mají nahrávky jednoho a téhož textu jednou s hlasitou výslovností (získali tedy EMG signál i smysluplné audio), podruhé s přesně tím stejným textem, ale potichu (získali jen EMG signál).

A teď tedy provedeme ten transfer learning. Pomocí nějaké chytré metody napasujeme na sebe EMG signál z hlasité řeči a EMG signál tiché řeči. Provedeme tedy alignment (zarovnání) EMG signálů hlasité řeči a tiché řeči. To se dá udělat pomocí metody **dynamic time warping** a ještě dalšího zpracování signálu, což v článku nebylo podrobně rozepsáno, spokojili se jenom s názvy metod, takže i my se spokojíme s názvy metod.

Takže my teď máme dvě užitečné věci: Jednak máme model, který nám pro EMG signál při hlasité řeči říká, jak mají vypadat klíčové vlastnosti audia; a jednak máme přesný návod, jak mezi sebou převést dva dosti podobné, jen trošku odlišné, EMG signály. Teď už nám nic nebrání, abychom pro každý kousek vstupního tichého EMG signálu zjistili, jak se tento kousek vztahuje k EMG signálu pro hlasitou řeč, a pro tento zase víme, které klíčové vlastnosti zvuku nám dává, protože jsme ho nahráli souběžně s audiem. Takže jsme získali upravený výstup, target, pro tichý EMG signál a můžeme se učit.

Metodologicky v článku asi moc nového není. Pravda ale je, že ozvučování silent speech je trochu díra na trhu a navíc na to transfer learning ještě nikdo neuplatnil, ani data k tomuto účelu předtím neexistovala. Také je pravda, že výsledky byly celkem ohromující, v jednom experimentu se chybovost na slovech zlepšila z 64% na 4% v absolutních číslech (!) v *closed vocabulary* úloze (malý, omezený slovníček), v dalších experimentech došlo mezi 88% až 68% k redukci relativní chyby na slovech v *open vocabulary* úloze (libovolný text, všechna slova jazyka).
