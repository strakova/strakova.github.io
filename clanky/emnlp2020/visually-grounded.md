# Visually Grounded Compound PCFGs

Yanpeng Zhao a Ivan Titov

EMNLP 2020 Honourable Mention

[Původní článek|https://www.aclweb.org/anthology/2020.emnlp-main.354/]

**Grounded NLP** je oblast, která studuje vztah mezi přirozeným jazykem a vnímáním a akcí ve světě. Všeobecně je to tedy spojení NLP a nějakých multimodálních dat.

**Visual grounding** je potom spojení NLP a „vision“, třeba NLP plus obrázky. Většinou jsou ty obrázky nějak propojené s textem, např. obrázky a jejich popisky.

**PCFGs** jsou **probabilistic context-free grammars** – pravidla mají pravděpodobnosti, že se uplatní.

Cílem je odvodit gramatiku, konkrétně PCFG, bez nápovědy, bez učitele (unsupervised), jenom ze slov. Chceme tedy provést **grammar induction from words**, odvození latentní gramatiky v neoznačeném textu. Doteď se to dělalo jen pomocí textu, teď chceme vyzkoušet, jestli by nám pomohla další informace z obrázků.

Už se o to pokusil článek z předchozího roku (Shi a kol., 2019), kteří měli k dispozici obrázky s popiskami.

Potíže byly dvě, a proto výsledky nebyly tak pěkné, jak bychom si přáli:

Zaprvé, a to je ta horší a více fundamentální obtíž, o PCFG gramatikách se už několik desítek let vědělo, že se nedají dobře naučit pomocí EM algoritmu. Teoreticky to pasuje dobře, ale ty výsledné gramatiky prostě nebyly dobré. V roce 2002 Manning a Klein empiricky ukázali, že pokud se snažíme automaticky odvodit PCFG gramatiku z textu pomocí tehdy dostupných optimalizátorů, všechny dopadnou hůř v lidské evaluaci, než jednoduchá baseline, kdybychom si vždycky vybrali „right-branching tree“ (syntaktický konstituenční stromeček se větví pořád doprava). Proč to tak je, to nikdo nevěděl. Vinilo se z toho EM, PCFG, optimalizátory, celkově se říkalo, že to je prostě „ill problem“ a hotovo.

Až přišli tady Kim, Dyer a Ruth v roce 2019 (teď mluvím o jiném článku) a řekli, pojďme zcela změnit způsob, jakým ten problém nalezení PCFG matematicky zadáváme, formulujeme. Neboli parametrizujeme. Doteď se počítalo tak, že každé pravidlo má nějakou pravděpodobnost, s níž se uplatní. Bylo to jedno číslo. A Kim a kol. si uvědomili, že všeobecně pomáhá sdílet a distribuovat informace. Takže změnili parametrizaci toho problému tak, že každé pravidlo nemá jedno izolované číslo pravděpodobnosti (**direct parametrization**), ale tato pravděpodobnost samotná je ještě sama parametrizovaná distribuovanou (sdílenou) reprezentací (**neural parametrization**), vlastně takovými embeddingy pro pravděpodobnosti. Pravděpodobnost, že se nějaké pravidlo uplatní, už není jedno číslo napočítané jako frekvence výskytu v korpusu, ale je to výsledek embeddování neuronovou sítí. V takové síti se informace sdílejí a jsou distribuované ve vektorech, nikoli izolované v jednotlivých číslech, napočítaných frekvenčně z korpusu. Jedna taková zavedená (a stará) teorie pro pravděpodobnosti parametrizované dalšími pravděpodobnostmi se jmenuje **compound probabilities** - a tak se název PCFGs obohatil o to slovo "compound".

Autoři současného článku tedy použili tuto metodu na indukci PCFGs z roku 2019 spolu s obrázky, taková kombinace loňských článků Kim et al 2019 (neural parametrization) a Shi et al 2019 (PCFG induction with visual grounding) a proto výsledky v článku tak moc poskočily.

Kromě toho ještě další potíž byla, že ne všechny konstituenty, které se snažíme vyvodit, mají zřejmou reprezentaci v obrázku. Tomu odpomohli autoři tím, že kromě trénování pouze na obrázcích s popisky ještě přidali jazykové modelování na velkých unlabeled datech. To mi jako tak velký výdobytek nepřijde a divím se tomu, proč se to nedělalo dřív.

Celkově mi mnohem pozoruhodnější přijde článek Kim, Dyer and Rush z ACL 2019.
