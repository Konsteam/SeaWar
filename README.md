class Dot:
    mimo_shot = "T"
    hit_shot = "X"
    svob_dot = "0"
    ship_dot = "#"
    ship_cont = "◘"

    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        return (self.x == other.x) and (self.y == other.y)


class Pole:
    def __init__(self, ship, visible=False):
        self.pole = [[Dot.svob_dot] * 6 for _ in range(6)]
        self.ships = []
        self.busy_dot = []
        self.visible = visible

    def print_pole(self):                                                       #print playing field
        for i in range(7):
            if i == 0:
                i = " "
            print (i, end = " ")
        print()
        for i in range(6):
            for j in range (6):
                if j == 0:
                    print(i + 1, self.pole[i][j], end=" ")
                else:
                    print(self.pole[i][j], end=" ")
            print()

    def add_ship (self, ship, visible=True):
        try:
            for dot in ship.ship_main():
                if 5 < dot.x < 0 or 5 < dot.y < 0:
                    if dot.x in self.busy_dot or dot.y in self.busy_dot:
                        raise IndexError
                self.pole[dot.x][dot.y] = dot.ship_dot
                self.ships.append(ship)
                self.busy_dot.append(ship.ship_main())
                self.busy_dot.append(ship.ship_cont(ship.ship_main()))
            return self.pole, self.ships, self.busy_dot
        except IndexError:
            if visible is True:
                print("Ошибка расположения")

class Ship:

    def __init__(self, x, y, size, comp, ship_dot=None):
        self.x = x
        self.y = y
        self.size = size
        self.ship_hp = size
        self.comp = comp
        self.ship_dot = ship_dot
        if ship_dot is None:
            ship_dot = []
        self.ship_contur = []

    def ship_main(self):
        self.ship_dot = []
        if self.comp == 1:
            for dot in range (self.size):
                self.ship_dot.append(Dot(self.x - 1  + dot, self.y - 1 ))
        else:
            for dot in range (self.size):
                self.ship_dot.append(Dot(self.x - 1, self.y - 1 + dot))
        return self.ship_dot

    def ship_cont(self, ship_dot):
        for dot in self.ship_dot:
            for i in range (dot.x - 1, dot.x + 2):
                for j in range (dot.y - 1, dot.y + 2):
                    if Dot (i, j) not in self.ship_contur and Dot (i, j) not in self.ship_dot and 0 <= i <= 5 and 0 <= j <= 5:
                        self.ship_contur.append(Dot (i, j))
        return self.ship_contur


ship1 = Ship(4, 4, 2, 1)
pole1 = Pole(ship1.ship_main(),ship1.ship_cont(ship1.ship_main()))
pole1.add_ship(ship1)
pole1.print_pole()

ship5 = Ship(1, 1, 3, 2)
pole1.add_ship(ship5)
pole1.print_pole()

if ship5 in pole1.ships:
    print ("Есть такой корабль")
else:
    print("Что-то не так")
