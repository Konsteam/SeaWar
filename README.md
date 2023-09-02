import random


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
    def __init__(self, visible=False):
        self.pole = [[Dot.svob_dot] * 6 for _ in range(6)]
        self.ships = []
        self.busy_dot = []
        self.shots = []
        self.visible = visible
        self.ships_hp = 0

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
        return self.pole

    def add_ship(self, ship, visible=True):
        try:
            for dot in ship.ship_main():
                if dot in self.busy_dot or dot.x < 0 or dot.x > 5 or dot.y < 0 or dot.y > 5:
                    raise IndexError
            for dot in ship.ship_main():
                if visible == True:
                    self.pole[dot.x][dot.y] = dot.ship_dot
                else:
                    self.pole[dot.x][dot.y] = dot.svob_dot
            self.ships.append(ship)
            self.ships_hp += ship.size
            self.busy_dot = self.busy_dot + ship.ship_main()
            self.busy_dot = self.busy_dot + ship.ship_cont(ship.ship_main())
            return self.pole, self.ships, self.busy_dot
        except IndexError:
            if visible is True:
                print("Ошибка расположения")
                return False
        return self.ships_hp

    def shot(self, cord, visible=True):
        try:
            shot = Dot((cord.x - 1), (cord.y - 1))
            if shot.x < 0 or (cord.x - 1) > 5 or (cord.y - 1) < 0 or (cord.y - 1) > 5 or Dot((cord.x - 1), (cord.y - 1)) in self.shots or \
                    self.pole[(cord.x - 1)][(cord.y - 1)] == Dot.ship_cont:
                raise IndexError
            try:                                                                                 # раскрыть список кораблей
                for ship in self.ships:                                                 # запросить точки каждого корабля
                    for dot in ship.ship_main():
                        if shot in ship.ship_main():                                    # если точка выстрела оказывается в списке точек корабля
                            self.pole[(cord.x - 1)][(cord.y - 1)] = Dot.hit_shot                  # поставить знак попадания по кораблю
                            self.ships_hp -= 1
                            ship.ship_hp -= 1
                            if ship.ship_hp == 0:
                                for dot in ship.ship_contur:
                                    self.pole[dot.x][dot.y] = Dot.ship_cont
                                    self.shots = self.shots + [Dot((cord.x - 1), (cord.y - 1))]
                            raise StopIteration
                        else:                                                                                               # если нет
                            self.pole[(cord.x - 1)][(cord.y - 1)] = Dot.mimo_shot                  # поставить знак промаха
                            self.shots = self.shots + [Dot((cord.x - 1), (cord.y - 1))]
            except StopIteration:
                pass
        except IndexError:
            if visible is True:
                print("Ошибка выстрела")
                raise IndexError
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
    def __init__(self,my_board, your_board):
        self.my_board = my_board
        self.your_board = your_board

    def ask_shot (sefl):
        cord = Dot
        try:
            x = int(input("строка"))
            y = int(input("столбец"))
        except TypeError:
            print("Ошибка ввода")
        return cord(x, y)

class II:
    def __init__(self, my_board, your_board):
        self.my_board = my_board
        self.your_board = your_board

    def ask_shot(sefl):
        cord = Dot
        x = random.randint(1,6)
        y = random.randint(1,6)
        return cord(x, y)

class Game:
    def __init__(self, shot=None):
        self.ship_size_list = [3, 2, 2, 1, 1, 1, 1]
        self.shot = shot

    def gen_human_pole(self):
        pole_H = Pole()
        pole_H.print_pole()
        ship_count = 0
        while ship_count != 7:
            x = int(input("Строка: "))
            y = int(input("Столбец: "))
            size = self.ship_size_list[ship_count]
            if size == 1:
                comp = 1
            else:
                comp = int(input("положение: "))
            ship = Ship(x,y,size,comp)
            if bool(pole_H.add_ship(ship)) == True:
                ship_count += 1
                pole_H.print_pole()
        else:
            return pole_H

    def gen_ii_pole(self):
        pole_ii = Pole()
        ship_count = 0
        ship_count2 = 0
        while ship_count2 != 7:
            x = random.randint(1,6)
            y = random.randint(1,6)
            size = self.ship_size_list[ship_count2]
            if size == 1:
                comp = 1
            else:
                comp = random.randint(1,2)
            ship = Ship(x,y,size,comp)
            if bool(pole_ii.add_ship(ship, visible=False)) == True:
                ship_count2 += 1
            else:
                ship_count += 1
                if ship_count > 100:
                    ship_count = 0
                    ship_count2 = 0
                    pole_ii = Pole()
                    return g.gen_ii_pole()
        else:
            return pole_ii

    def star(self,pole_h,pole_ii):
        self.pole_h = pole_h
        self.pole_ii = pole_ii
        self.pole_h.print_pole()
        self.pole_ii.print_pole()
        print("Начинаем игру!!!")

    def gaming (self):
        ai = II(self.pole_ii, self.pole_h)
        pl = Player(self.pole_h,self.pole_ii)
        while True:
            while True:
                try:
                    self.pole_ii.shot(pl.ask_shot())
                    break
                except IndexError:
                    print("Неверные координаты")
                    self.pole_ii.print_pole()
                except ValueError:
                    print("Неверный выстрел")
                    self.pole_ii.print_pole()
            print("Поле ИИ")
            self.pole_ii.print_pole()
            while True:
                try:
                    self.pole_h.shot(ai.ask_shot(), visible=False)
                    break
                except IndexError:
                    None
                except ValueError:
                    None
            print("Поле игрока")
            self.pole_h.print_pole()
            if self.pole_ii.ships_hp == 0:
                print("Восстания машин не будет!")
                break
            if self.pole_h.ships_hp == 0:
                print("Человечество будет уничтожено!")
                break

g = Game()
print( """Напутствия перед игрой:
Корабли надо расставлять на расстоянии 1 клетки друг от друга
Размер кораблей при расстановке задан в следующем порядке:
3-х полубный 2-палубный 2-палубный 1-палубный 1-палубный 1-палубный 1-палубный
для горизонтального располжения корабля значение расположения 2
для вертикального располжения корабля значение расположения 1
Против Вас будет играть ИИ
Пебеждает тот кто первым уничтожит все корабли проивника
                """)
g.star(g.gen_human_pole(),g.gen_ii_pole())
g.gaming()
