import pygame as p #Eine Bibliothek von Python für die Entwicklung von Multimedia-Anwendungen   #übernommen
import SchachStandAktualisierung   
import pygame_widgets
from pygame_widgets.button import Button

HÖHE = 515 #Höhe und Breite in Pixel    #übernommen
BREITE = 800#übernommen
DIMENSION = 8 #Ein Schachbrett hat 8*8=64 Felder.#übernommen
V_GRÖßE = HÖHE // DIMENSION #512/8=64. Ein Feld hat die Länge und die Breite von 64 Pixel.  #übernommen
BILDER = {} #Dictionary, indem später die Schachfirguren gespeichert werden #übernommen

def ladeBilder():#übernommen
    figuren = ['gT', 'gP', 'gL', 'gD', 'gK', 'gB', 'wT', 'wP', 'wL', 'wD', 'wK', 'wB']
    for figur in figuren:
        #Die Bilder werden vom Ordner geladen und sklaiert. Sie bekommen die Länge und die Breite von 64 Pixel. 
        #Im Dictionary werden sie mir ihrer jeweiligen Bezeichnung abgespeichert.
        BILDER[figur] = p.transform.scale(p.image.load("Bilder/" + figur + ".png"), (V_GRÖßE, V_GRÖßE))

def main():

    p.init() #Diese Zeile kann weggelassen werden.  #übernommen
    bildschirm = p.display.set_mode((BREITE, HÖHE)) #Es wird ein sichtbares Fenster mit ihrer jeweiligen Höhe und Breite erstellt.  #übernommen
    sps = SchachStandAktualisierung.SpielStand() #Diese Klasse kontrolliert den Spielstatus und die Züge der einzelnen Figuren  #übernommen
    ladeBilder() #Die Bilder werden geladen.  #übernommen
    läuft = True #So lange der Spieler das Spiel nicht beendet, bleibt dieser Wert True.  #übernommen
    ausgewähltesRechteck = () #In diesem Tupel werden die Koordinaten des ausgewählten Rechtecks gespeichert. Immer nur von einem Rechteck  #übernommen
    spielerKlicks = [] #Hier werden auch die Koordinaten des ausgewählten Rechtecks gespeichert, jedoch maximal von zwei.  #übernommen
    spalte, zeile = 0,0#übernommen
    aktualisiere = False#übernommen
    zeichneSpielstand(bildschirm, sps)#übernommen
    p.display.flip()#übernommen


    #Der Text für die Tore der KI werden angezeigt
    tfont = p.font.SysFont("monospace", 30)
    SCHWARZ = (0, 0, 0)

    taste = Button(#übernommen
        # Mandatory Parameters
        bildschirm,  # Surface to place taste on
        565,  # X-coordinate of top left corner
        230,  # Y-coordinate of top left corner
        80,  # Width
        40,  # Height

        # Optional Parameters
        text='Neustart',  # Text to display
        fontSize=20,  # Size of font
        margin=20,  # Minimum distance between text/image and edge of taste
        inactiveColour=(100, 230, 0),  # Colour of taste when not being interacted with
        hoverColour=(150, 0, 0),  # Colour of taste when being hovered over
        pressedColour=(0, 200, 20),  # Colour of taste when being clicked
        radius=20,  # Radius of border corners (leave empty for not curved)
        onClick=lambda: starteSpielErneutOderBeende('j',sps)  # Function to call when clicked on
    )

    taste2 = Button(#übernommen
        # Mandatory Parameters
        bildschirm,  # Surface to place taste on
        665,  # X-coordinate of top left corner
        230,  # Y-coordinate of top left corner
        80,  # Width
        40,  # Height

        # Optional Parameters
        text='Beenden',  # Text to display
        fontSize=20,  # Size of font
        margin=20,  # Minimum distance between text/image and edge of taste
        inactiveColour=(100, 230, 0),  # Colour of taste when not being interacted with
        hoverColour=(150, 0, 0),  # Colour of taste when being hovered over
        pressedColour=(0, 200, 20),  # Colour of taste when being clicked
        radius=20,  # Radius of border corners (leave empty for not curved)
        onClick=lambda: starteSpielErneutOderBeende('n',sps)  # Function to call when clicked on
    )

    while läuft:#übernommen
        if aktualisiere:#übernommen

            startZeile = spielerKlicks[0][0]#übernommen
            startSpalte = spielerKlicks[0][1]#übernommen
            endZeile = spielerKlicks[1][0]#übernommen
            endSpalte = spielerKlicks[1][1]#übernommen
            bewegteFigur = sps.spielbrett[startZeile][startSpalte]#übernommen 

            sps.definiereZugAttribute(startZeile, startSpalte, endZeile, endSpalte, bewegteFigur)#übernommen

            if(sps.kontrolliere() == True):

                if sps.zugWeiß:#Die Variable zugWeiß wurde übernommen und es wird an vielen Stellen im Code verwendet.
                    if endZeile == 0 and endSpalte == 3:# Es wird nicht jedes Mal gekenzzeichnet werden, weil es oft vorkommt.
                        sps.Tor = True
                    elif endZeile == 0 and endSpalte == 4:
                        sps.Tor = True
                elif not sps.zugWeiß:
                    if endZeile == 7 and endSpalte == 3:
                        sps.Tor = True
                    elif endZeile == 7 and endSpalte == 4:
                        sps.Tor = True

                aktualisiereFelder(bildschirm, sps)#übernommen
                sps.macheZug()#übernommen 
                sps.zugWeiß = not sps.zugWeiß#übernommen

                if sps.kontrolliereZugMöglichkeit() == False:
                    sps.spielBeendet = True
                    
                textKITore = tfont.render(str(sps.ToreSchwarz), 30, SCHWARZ)
                bildschirm.fill(p.Color("white"), (647, 60, 50, 30))
                bildschirm.blit(textKITore, (647, 60)) 

                textSpTore = tfont.render(str(sps.ToreWeiß), 30, SCHWARZ)
                bildschirm.fill(p.Color("white"), (647, 410, 50, 30))
                bildschirm.blit(textSpTore, (647, 410)) 
            else:
                print("Dieser Zug kann nicht ausgeführt werden!")
           

            ausgewähltesRechteck = ()#übernommen 
            spielerKlicks = []#übernommen
            aktualisiere = False#übernommen
                
            p.display.update() #Nur der notwendige Teil des Displays soll aktualisiert werden.
        
        else:
            for e in p.event.get():#übernommen

                pygame_widgets.update(e)
                p.display.update()#übernommen 
                if e.type == p.QUIT:#übernommen
                    läuft = False#übernommen
                                               
                elif sps.spielBeendet == False:
                    if e.type == p.MOUSEBUTTONDOWN:#übernommen
                        position = p.mouse.get_pos()#übernommen
                        if position[0] < 512 and position[1] < 512:
                            spalte = position[0] // V_GRÖßE#übernommen
                            zeile = position[1] // V_GRÖßE#übernommen
                            if (ausgewähltesRechteck == (zeile, spalte)):#übernommen
                                ausgewähltesRechteck = ()#übernommen
                                spielerKlicks = []#übernommen
                            elif (len(spielerKlicks) == 0 and sps.spielbrett[zeile][spalte] == '--'):
                                ausgewähltesRechteck = ()#übernommen 
                                spielerKlicks = []#übernommen
                            elif (len(spielerKlicks) == 0 and sps.spielbrett[zeile][spalte][:1] == 'g' and sps.zugWeiß):
                                ausgewähltesRechteck = ()#übernommen 
                                spielerKlicks = []#übernommen 
                            elif (len(spielerKlicks) == 0 and sps.spielbrett[zeile][spalte][:1] == 'w' and not sps.zugWeiß):
                                ausgewähltesRechteck = ()#übernommen 
                                spielerKlicks = []#übernommen 
                            else:
                                ausgewähltesRechteck = (zeile, spalte)#übernommen
                                spielerKlicks.append(ausgewähltesRechteck)#übernommen
                            if len(spielerKlicks) == 2:#übernommen 
                                aktualisiere = True

                    elif sps.starteOderBeendeSpiel == 'j':
                        sps.spielBeendet = False
                        sps = SchachStandAktualisierung.SpielStand()#übernommen
                        bildschirm = p.display.set_mode((BREITE, HÖHE))#übernommen
                        ausgewähltesRechteck = ()#übernommen 
                        spielerKlicks = [] #übernommen
                        spalte, zeile = 0,0#übernommen
                        zeichneSpielstand(bildschirm, sps)#übernommen
                    
                    elif sps.starteOderBeendeSpiel == 'n':
                            läuft = False
                 
                else:
                    if sps.spielBeendet == True:
                        aktualisiere = False
                        bildschirm.fill(p.Color("white"), (520, 0, 500, 800))

                        if sps.ToreWeiß > sps.ToreSchwarz:

                            textSpielergebnis = tfont.render("Sie haben das", 20, SCHWARZ)
                            bildschirm.blit(textSpielergebnis, (520, 10)) 
                            
                            textSpielergebnis2 = tfont.render("Spiel mit " +str(sps.ToreWeiß) + ":" + str(sps.ToreSchwarz), 20, SCHWARZ)
                            bildschirm.blit(textSpielergebnis2, (520, 50)) 

                            textSpielergebnis3 = tfont.render("gewonnwn", 20, SCHWARZ)
                            bildschirm.blit(textSpielergebnis3, (520, 90)) 
                           
                        elif sps.ToreSchwarz > sps.ToreWeiß:

                            textSpielergebnis = tfont.render("Die KI hat das", 20, SCHWARZ)
                            bildschirm.blit(textSpielergebnis, (520, 10)) 
                            
                            textSpielergebnis2 = tfont.render("Spiel mit " +str(sps.ToreSchwarz) + ":" + str(sps.ToreWeiß), 20, SCHWARZ)
                            bildschirm.blit(textSpielergebnis2, (520, 50)) 

                            textSpielergebnis3 = tfont.render("gewonnwn", 20, SCHWARZ)
                            bildschirm.blit(textSpielergebnis3, (520, 90)) 
                    
                        else:
                            textSpielergebnis = tfont.render("Das Spiel ist", 30, SCHWARZ)
                            bildschirm.blit(textSpielergebnis, (520, 10)) 
                            
                            textSpielergebnis2 = tfont.render("unentschieden!", 30, SCHWARZ)
                            bildschirm.blit(textSpielergebnis2, (520, 50)) 

                        if sps.starteOderBeendeSpiel == 'j':
        
                            sps.spielBeendet = False
                            sps = SchachStandAktualisierung.SpielStand()#übernommen
                            bildschirm = p.display.set_mode((BREITE, HÖHE))#übernommen
                            ausgewähltesRechteck = ()#übernommen 
                            spielerKlicks = []#übernommen 
                            spalte, zeile = 0,0#übernommen
                            zeichneSpielstand(bildschirm, sps)#übernommen

                        elif sps.starteOderBeendeSpiel == 'n':
                            läuft = False

def starteSpielErneutOderBeende(c, sps):   
    if c == 'j':            
        sps.starteOderBeendeSpiel = c 
    else:
        sps.starteOderBeendeSpiel = c 


def zeichneSpielstand(bildschirm, sps): #übernommen 
    zeichneBrett(bildschirm)#übernommen
    zeichneFiguren(bildschirm, sps.spielbrett)#übernommen 

    #Der Text für die Tore der KI werden angezeigt
    tfont = p.font.SysFont("monospace", 30) #Ab dieser Stelle, wurde nichts in dieser Methode übernommen
    SCHWARZ = (0, 0, 0)
    textKIS = tfont.render("Die KI hat", 100, SCHWARZ)
    bildschirm.blit(textKIS, (565, 20))

    textKIS2 = tfont.render("Tore", 30, SCHWARZ)
    bildschirm.blit(textKIS2, (620, 100))

    textSPS = tfont.render("Sie haben", 100, SCHWARZ)
    bildschirm.blit(textSPS, (575, 370))

    textSPS2 = tfont.render("Tore", 30, SCHWARZ)
    bildschirm.blit(textSPS2, (620, 450))

    textKITore = tfont.render(str(sps.ToreSchwarz), 30, SCHWARZ)
    bildschirm.blit(textKITore, (647, 60)) 

    textSPTore = tfont.render(str(sps.ToreWeiß), 30, SCHWARZ)
    bildschirm.blit(textSPTore, (647, 410)) 

def zeichneBrett(bildschirm):#übernommen
    spalteors = [p.Color("white"), p.Color("lightcoral")]#übernommen
    for r in range(DIMENSION):#übernommen
        for c in range(DIMENSION):#übernommen
            spalteor = spalteors[((r+c) % 2)]#übernommen
            p.draw.rect(bildschirm, spalteor, p.Rect(c*V_GRÖßE, r*V_GRÖßE, V_GRÖßE, V_GRÖßE))#übernommen

    p.draw.rect(bildschirm, p.Color("white"), p.Rect(512, 0, 288, 512))#selbst gemacht


def zeichneFiguren(bildschirm, spielbrett):#übernommen
    for r in range(DIMENSION):#übernommen
        for c in range(DIMENSION):#übernommen
            figur = spielbrett[r][c]#übernommen 
            if figur != "--":#übernommen 
                bildschirm.blit(BILDER[figur], p.Rect(c*V_GRÖßE, r*V_GRÖßE, V_GRÖßE, V_GRÖßE))#übernommen

def aktualisiereFelder(bildschirm, sps):

    spielbrett = sps.spielbrett
    startZeile = sps.startZeile
    startSpalte = sps.startSpalte
    endZeile = sps.endZeile
    endSpalte = sps.endSpalte

    spalteors = [p.Color("white"), p.Color("lightcoral")]#übernommen
    spalteorE = spalteors[((endZeile+endSpalte) % 2)]#übernommen 
    spalteorS = spalteors[((startZeile+startSpalte) % 2)]#übernommen 

    if sps.Tor:
        p.draw.rect(bildschirm, spalteorE, p.Rect(endSpalte*V_GRÖßE, endZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE))  
        p.draw.rect(bildschirm, spalteorS, p.Rect(startSpalte*V_GRÖßE, startZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

    elif sps.enPassantFürBauerWeiß:
        sps.enPassantFelderFürBauerWeiß.remove([(startZeile, startSpalte),(endZeile, endSpalte)])  
        
        if not spielbrett[endZeile][endSpalte] == '--':  
            p.draw.rect(bildschirm, spalteorE, p.Rect(endSpalte*V_GRÖßE, endZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

        figur = spielbrett[startZeile][startSpalte]#übernommen
        if figur != "--": 
            bildschirm.blit(BILDER[figur], p.Rect(endSpalte*V_GRÖßE, endZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE))

        p.draw.rect(bildschirm, spalteorS, p.Rect(startSpalte*V_GRÖßE, startZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE))  

        if spalteorS == p.Color("white"):
            enPassantFarbe = p.Color("lightcoral")
        else:
            enPassantFarbe = p.Color("white")

        p.draw.rect(bildschirm, enPassantFarbe, p.Rect(endSpalte*V_GRÖßE, (endZeile+1)*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

    elif sps.enPassantFürBauerSchwarz:
        sps.enPassantFelderFürBauerSchwarz.remove([(startZeile, startSpalte),(endZeile, endSpalte)])  

        if not spielbrett[endZeile][endSpalte] == '--': 
            p.draw.rect(bildschirm, spalteorE, p.Rect(endSpalte*V_GRÖßE, endZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

        figur = spielbrett[startZeile][startSpalte]#übernommen
        if figur != "--": 
            bildschirm.blit(BILDER[figur], p.Rect(endSpalte*V_GRÖßE, endZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE))
 
        p.draw.rect(bildschirm, spalteorS, p.Rect(startSpalte*V_GRÖßE, startZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 
        spalteor = spalteors[((endZeile-1+endSpalte) % 2)] 
        p.draw.rect(bildschirm, spalteor, p.Rect(endSpalte*V_GRÖßE, (endZeile-1)*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

    elif sps.neueFigurFürBauern:        
        if spielbrett[endZeile][endSpalte][:1] == spielbrett[startZeile][startSpalte][:1]: 
            p.draw.rect(bildschirm, spalteorS, p.Rect(startSpalte*V_GRÖßE, startZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 
            figur = spielbrett[endZeile][endSpalte]#übernommen
            if figur != "--": 
                bildschirm.blit(BILDER[figur], p.Rect(startSpalte*V_GRÖßE, startZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE))
        else:
            print("Es darf keine gegnerische Figur ausgewählt werden.")

    elif sps.rochadeKleinWeiß:
            spalteor = spalteors[((7+7) % 2)] 
            p.draw.rect(bildschirm, spalteor, p.Rect(7*V_GRÖßE, 7*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

            spalteor = spalteors[((7+4) % 2)] 
            p.draw.rect(bildschirm, spalteor, p.Rect(4*V_GRÖßE, 7*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

            figur1 = spielbrett[7][7]#übernommen
            figur2 = spielbrett[7][4]#übernommen

            bildschirm.blit(BILDER[figur1], p.Rect((5)*V_GRÖßE, 7*V_GRÖßE, V_GRÖßE, V_GRÖßE))
            bildschirm.blit(BILDER[figur2], p.Rect((6)*V_GRÖßE, 7*V_GRÖßE, V_GRÖßE, V_GRÖßE))

    elif sps.rochadeGroßWeiß:
            spalteor = spalteors[((7+0) % 2)] 
            p.draw.rect(bildschirm, spalteor, p.Rect(0*V_GRÖßE, 7*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

            spalteor = spalteors[((7+4) % 2)] 
            p.draw.rect(bildschirm, spalteor, p.Rect(4*V_GRÖßE, 7*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

            figur1 = spielbrett[7][0]#übernommen
            figur2 = spielbrett[7][4]#übernommen

            bildschirm.blit(BILDER[figur1], p.Rect((3)*V_GRÖßE, 7*V_GRÖßE, V_GRÖßE, V_GRÖßE))
            bildschirm.blit(BILDER[figur2], p.Rect((2)*V_GRÖßE, 7*V_GRÖßE, V_GRÖßE, V_GRÖßE))

    elif sps.rochadeKleinSchwarz:
            spalteor = spalteors[((0+7) % 2)] 
            p.draw.rect(bildschirm, spalteor, p.Rect(7*V_GRÖßE, 0*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

            spalteor = spalteors[((4) % 2)] 
            p.draw.rect(bildschirm, spalteor, p.Rect(4*V_GRÖßE, 0*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

            figur1 = spielbrett[0][7]#übernommen
            figur2 = spielbrett[0][4]#übernommen

            bildschirm.blit(BILDER[figur1], p.Rect((5)*V_GRÖßE, 0*V_GRÖßE, V_GRÖßE, V_GRÖßE))
            bildschirm.blit(BILDER[figur2], p.Rect((6)*V_GRÖßE, 0*V_GRÖßE, V_GRÖßE, V_GRÖßE))

    elif sps.rochadeGroßSchwarz: 
            spalteor = spalteors[((0+0) % 2)] 
            p.draw.rect(bildschirm, spalteor, p.Rect(0*V_GRÖßE, 0*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

            spalteor = spalteors[((0+4) % 2)] 
            p.draw.rect(bildschirm, spalteor, p.Rect(4*V_GRÖßE, 0*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

            figur1 = spielbrett[0][0]#übernommen
            figur2 = spielbrett[0][4]#übernommen

            bildschirm.blit(BILDER[figur1], p.Rect((3)*V_GRÖßE, 0*V_GRÖßE, V_GRÖßE, V_GRÖßE))
            bildschirm.blit(BILDER[figur2], p.Rect((2)*V_GRÖßE, 0*V_GRÖßE, V_GRÖßE, V_GRÖßE))

    else:

        #Immer im wechsel 0 und 1 als Ergebnis
        p.draw.rect(bildschirm, spalteorE, p.Rect(endSpalte*V_GRÖßE, endZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

        figur = spielbrett[startZeile][startSpalte]#übernommen
        if figur != "--": 
            bildschirm.blit(BILDER[figur], p.Rect(endSpalte*V_GRÖßE, endZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE))

        #Immer im wechsel 0 und 1 als Ergebnis
        p.draw.rect(bildschirm, spalteorS, p.Rect(startSpalte*V_GRÖßE, startZeile*V_GRÖßE, V_GRÖßE, V_GRÖßE)) 

if __name__ == "__main__":
    main()
