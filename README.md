class Dot:
    mimo_shot = "T"
    hit_shot = "X"
    svob_dot = "0"
    ship_dot = "#"
    ship_cont = "♦"

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
        self.shots = []
        self.visible = visible

    def print_pole(self):  # print playing field
        for i in range(7):
            if i == 0:
                i = " "
            print(i, end=" ")
        print()
        for i in range(6):
            for j in range(6):
                if j == 0:
                    print(i + 1, self.pole[i][j], end=" ")
                else:
                    print(self.pole[i][j], end=" ")
            print()

    def add_ship(self, ship, visible=True):
        try:
            for dot in ship.ship_main():
                if dot in self.busy_dot or dot.x < 0 or dot.x > 5 or dot.y < 0 or dot.y > 5:
                    raise IndexError
            for dot in ship.ship_main():
                self.pole[dot.x][dot.y] = dot.ship_dot
            self.ships.append(ship)
            self.busy_dot = self.busy_dot + ship.ship_main()
            self.busy_dot = self.busy_dot + ship.ship_cont(ship.ship_main())
            return self.pole, self.ships, self.busy_dot
        except IndexError:
            if visible is True:
                print("Ошибка расположения")

    def shot(self, x, y, visible=True):
        try:
            shot = Dot((x - 1), (y - 1))
            if shot.x < 0 or (x - 1) > 5 or (y - 1) < 0 or (y - 1) > 5 or Dot((x - 1), (y - 1)) in self.shots:
                raise IndexError
            try:  # раскрыть список кораблей
                for ship in self.ships:  # запросить точки каждого корабля
                    for dot in ship.ship_main():
                        if shot in ship.ship_main():  # если точка выстрела оказывается в списке точек корабля
                            self.pole[(x - 1)][(y - 1)] = Dot.hit_shot  # поставить знак попадания по кораблю
                            ship.ship_hp -= 1
                            if ship.ship_hp == 0:
                                for dot in ship.ship_contur:
                                    self.pole[dot.x][dot.y] = Dot.ship_cont
                            raise StopIteration
                        else:  # если нет
                            self.pole[(x - 1)][(y - 1)] = Dot.mimo_shot  # поставить знак промаха
            except StopIteration:
                pass
            self.shots = self.shots + [Dot((x - 1), (y - 1))]
        except IndexError:
            if visible is True:
                print("Ошибка выстрела")
        return self.shots


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
            for dot in range(self.size):
                self.ship_dot.append(Dot(self.x - 1 + dot, self.y - 1))
        else:
            for dot in range(self.size):
                self.ship_dot.append(Dot(self.x - 1, self.y - 1 + dot))
        return self.ship_dot

    def ship_cont(self, ship_dot):
        for dot in self.ship_dot:
            for i in range(dot.x - 1, dot.x + 2):
                for j in range(dot.y - 1, dot.y + 2):
                    if Dot(i, j) not in self.ship_contur and Dot(i,j) not in self.ship_dot and 0 <= i <= 5 and 0 <= j <= 5:
                        self.ship_contur = self.ship_contur + [Dot(i, j)]
        return self.ship_contur

class Player:
    def __init__(self, player):
        self.player = player

    def ask (sefl):
        try:
            x = input("строка")
            y = input("столбец")
            if not (x.isdigit()) or not (y.isdigit()):
                raise TypeError
        except TypeError:
            print("Ошибка ввода")
        return x, y

class Game:
    def __init__(self):
        self.player = player
        self.ship_size_list = [3, 2, 2, 1, 1, 1, 1]
        self.ship_name = ["ship1","ship2","ship3","ship4","ship5","ship6","ship7"]
        self.name = 0

    def gen_human_pole(self):
        for ship in self.ship_size_list:
            if self.ship_size_list[ship] > 1:
                self.ship_name[self.name] = Ship ((int(input("Строка: "))),(int(input("Столбец: "))),\
                                        self.ship_size_list[self.ships], (int(input("Положение: "))))
            else:
                self.ship_name[self.name] = Ship((int(input("Строка: "))), (int(input("Столбец: "))), \
                                                 self.ship_size_list[self.ships], 1)
            pole_H.add_ship(self.ship_name[self.name])
            self.name += 1
        pole_H.print_pole()


g = Game
g.gen_human_pole()
