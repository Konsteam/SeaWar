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
    def __init__(self, ship_dot, ship_conturs, pole=None, ships=None, visible=False):
        self.pole = [[Dot.svob_dot] * 6 for _ in range(6)]
        if ships is None:
            ships = []
        self.ships = ships
        self.ship_dot = ship_dot
        self.visible = visible
        self.ship_conturs = ship_conturs
        self.tap_ship = []
        self.shots = []

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


    def add_ship(self, ship_dot, ship_conturs, visible=False):
        try:
            for _ in self.ship_dot:
                if _ in self.ships or _ in self.ship_conturs or _.x < 0 or _.x > 5 or _.y < 0 or _.y > 5:
                    raise IndexError
            self.ships = self.ships + self.ship_dot
            if visible is False:
                for _ in self.ship_dot:
                    self.pole[_.x][_.y] = _.ship_dot
                for _ in self.ship_conturs:
                    self.ship_conturs = self.ship_conturs + [_]
                return self.pole, self.ships, self.ship_conturs
        except IndexError:
            if visible is False:
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
            for _ in range (self.size):
                self.ship_dot = self.ship_dot + [Dot(self.x - 1  + _ , self.y - 1 )]
        else:
            for _ in range (self.size):
                self.ship_dot = self.ship_dot + [Dot(self.x - 1, self.y - 1 + _)]
        return self.ship_dot

    def ship_cont(self, ship_dot):
        for _ in self.ship_dot:
            for i in range ((_.x - 1), (_.x + 2)):
                for j in range ((_.y - 1), (_.y + 2)):
                    if Dot (i, j) not in self.ship_contur and Dot (i, j) not in ship_dot and 0 <= i <= 5 and 0 <= j <= 5:
                        self.ship_contur = self.ship_contur + [Dot (i, j)]
        return  self.ship_contur


ship1 = Ship(4, 4, 3, 1)
ship1.ship_main()
ship1.ship_cont(ship1.ship_main())
pole1 = Pole(ship1.ship_main(),ship1.ship_cont(ship1.ship_main()))
pole1.add_ship(ship1.ship_cont,ship1.ship_main)
pole1.print_pole()

ship2 = Ship(2, 2, 2, 1)
ship2.ship_main()
ship2.ship_cont(ship2.ship_main())
pole1 = Pole(ship2.ship_main(),ship2.ship_cont(ship2.ship_main()))
pole1.add_ship(ship2.ship_cont,ship2.ship_main)
pole1.print_pole()

ship3 = Ship(1, 6, 1, 1)
ship3.ship_main()
ship3.ship_cont(ship3.ship_main())
pole1 = Pole(ship3.ship_main(),ship3.ship_cont(ship3.ship_main()))
pole1.add_ship(ship3.ship_cont,ship3.ship_main)
pole1.print_pole()

if ship1 in pole1.ships:
    print ("Есть такой корабль")
else:
    print("Что-то не так")
