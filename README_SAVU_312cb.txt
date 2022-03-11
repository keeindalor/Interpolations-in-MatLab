-----README TEMA METODE NUMERICE, IONUT-CRISTIAN SAVU, 312CB-----
Un scurt feedback:
*Tema foarte interesanta, mi-a placut enorm sa aflu ca, de fapt, imaginile se interpoleaza astfel, pentru niciodata
nu mi-am pus intrebarea aceasta, si mai ales faptul ca a imbinat materia Metode Numerice cu exemple din viata de zi cu zi
*Informatia din PDF si scheletul de cod au fost foarte bine structurate
*Un mare +: timpul foarte lung de realizare a temei


Interpolarea NN_Resize
-Pentru NN_2x2, am procedat astfel:
*in interiorul celor doua foruri care parcurg matricea, m-am folosit de distanta dintre puncte
astfel, daca modulul diferentelor dintre x-ul indicelui si x1 era mai mic decat cel al diferentei dintre x si x2,
x-ul indicelui il lua pe x1, iar in caz contrar, pe x2.
*analog pentru y
*in imaginea finala, pe pozitiile i si j am introdus indicii x_int(i) si y_int(j) din imagine

-NN_2x2_RGB
*am extras filtrele de culoare (am descoperit functia imread in interiorul checkerului)
*am aplicat functia NN_2x2 pe toate 3, iar la final am concatenat cu functia cat
(Am procedat identic la functiile RGB pentru urmatoarele interpolari, nu le mai mentionez)

-NN_Resize
*am calculat factorii de scalare s_x si s_y, matricea de transformare (din PDF), si inversa sa
*in interiorul forului, am rezolvat sistemul si am extras x_p si y_p, pe care ii trec in coordonatele 1-n
*le-am rotunjit 
*in matricea finala, am introdus pe pozitiile y+1 si x+1 (deoarece trebuia sa trecem de la 0-n-1 la 1-n) pixelii din 
imagine de pe pozitiile y_p si x_p (si nu x_p si y_p deoarece matricele sunt M*N, si nu N*M)
la final, am facut conversie la uint8, pentru a pastra imaginea

Interpolarea Bilineara
-Surrounding points"
*am aproximat prin reducere pe x si pe y, dupa care le-am adunat o unitate, pentru a fi x1<x<x2, y1<y<y2
*in cazul in care y1 sau x1 se aflau la margine, adica pe M sau N, din y2 respectiv x2 scadeam o unitate pentru a nu depasi conturul

-Bilinear Coef
*am calculat matricea interpolarii (PDF) + conversie la double
*am construit vectorul b (PDF) + conversie la double
*am rezolvat sistemul + conversie la double

-Bilinear 2x2
*am calculat coeficientii de interpolare folosind bilinear coef
*in for, am construit matricea finala folosind formula a0 + a1x + a2y + a3xy, folosind x_int si y_int de indicii i si j
*conversie la uint8 pentru a pastra imaginea

-Bilinear resize
*am calculat factorii de scalare s_X si s_y
*am calculat matricea si inversa matricii de transformare
*in for, am rezolvat acelasi sistem ca la NN_resize
*am aflat punctele inconjuratoare, pe care le folosesc pentru a calcula coeficientii de interpolare bilineara
*am calculat valoarea interpolata a pixelului folosind aceeasi formula ca al bilinear 2x2 dar cu solutiile sistemului matricii de transformare
*am facut conversia la uint8

-Bilinear rotate
*aici, am calculat valoarea sinusului si cosinusului unghiului de rotatie
*am initializat matricea de rotatie [c -s; s c] si inversa
*in for, am rezolvat sistemul
*in plus, daca solutiile sistemului depaseau dimensiunile matricii, pixelul devenea negru, adica ii dadeam valoarea 0
mai exact, daca x<1 sau x > n, identic pentru y
*am calculat punctele din jurul (x_p, y_p) si coeficientii bilineari
*am calculat pixelul folosind aceeasi formula
*conversie la uint8

Interpolarea bicubica
-Surrounding points
*acelasi ca la bilinear

-Bicubic coef
*am calculat matricile intermediare A1 si A3, mai exact cele din PDF
*am construit matricea de functii ca in PDF
*le-am facut conversie la Double pentru a putea calcula matricea finala A = A1 * Mat_F * A3

-Derivatele partiale
*voi mentiona ca pe toate 3 le-am calculat cu formulele aferente din PDF

-Precalc_D
*aceasta functie a calculat matricile derivatelor partiale ale functiei
*am initializat cele trei matrice cu 0-uri
*in interiorul forului, m-am asigurat ca in cazul in care indicii care parcurg dimensiunea matricei imaginii sunt egali cu marginile (1, sau M pentru x, N respectiv pentru y)
elementele de pe matricea precalculata sa fie 0, iar in caz contrar, adica daca ne aflam in interiorul matricei, sa obtinem pe acea pozitie
valoarea derivatei partiale in punctele (y,x)
(initial, am facut aceasta constructie de matrici precalculate folosindu-ma de trei foruri, pana sa imi dau seama ca este suficient unul singur)

-Bicubic Resize
*am calculat factorii de scalare
*am calculat matricile de transformare si inversa acesteia
*am precalculat matricile derivatelor
*in for, am rezolvat sistemul matricei de transformare
*am trecut coordonatele la sistemul nostru
*am calculat punctele inconjuratoare
*am calculat coeficientii bicubici
*am trecut coordonatele x_p y_p in patratul unitate
*am calculat patratul si cubul lui x_p si y_p pentru a interpola in final, folosindu-ne de functia care rezolva, in mod normal, sistemul de 16 ecuatii
*am introdus in imagine valoarea finala a pixelului
* am convertit la uint8