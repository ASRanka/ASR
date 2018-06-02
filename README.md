import turtle
import os
import math
import random
import Tkinter
import pygame, sys
from random import randrange as rand


def setting():
    master = Tkinter.Tk()
    master.geometry("%dx%d+0+0" % (500, 500))
    label = Tkinter.Label(master, text="SETTING", fg="red", font=("Arial", 50)).pack(side=Tkinter.TOP, expand=Tkinter.YES, fill=Tkinter.X)
    b = Tkinter.Button(master, text="Sound on", command=CallBack).pack(side=Tkinter.TOP, expand=Tkinter.YES, fill=Tkinter.X)
    a = Tkinter.Button(master, text="Sound off", command=quit).pack(side=Tkinter.TOP, expand=Tkinter.YES, fill=Tkinter.X)
    a = Tkinter.Button(master, text="Back", command=main).pack(side=Tkinter.BOTTOM, expand=Tkinter.YES, fill=Tkinter.X)

    Tkinter.mainloop()


def CallBack():
    # setting up a screen
    a = turtle.Screen()
    a.bgcolor("black")
    a.title("Space Game")



    # drawing border
    border_pen = turtle.Turtle()  # pen is for creatin a pen in turtle
    border_pen.speed(0)  # 0 is the maximum speed
    border_pen.color("white")
    border_pen.penup()
    border_pen.setposition(-300, -300)  # pen original position is (0,0)
    border_pen.pendown()
    border_pen.pensize(10)
    for i in range(4):  # i = side
        border_pen.fd(600)  # fd is for forward
        border_pen.lt(90)  # lt is for left
    border_pen.hideturtle()

    # setting the score to 0
    score = 0

    # drawing the score
    score_pen = turtle.Turtle()
    score_pen.speed(0)
    score_pen.color("white")
    score_pen.penup()
    score_pen.setposition(-290, 270)
    scorestring = "score %s" % score
    score_pen.write(scorestring, False, align="left", font=("Arial", 15, "normal"))
    score_pen.hideturtle()

    # createing a player
    player = turtle.Turtle()
    player.color("red")
    player.shape("triangle")
    player.penup()
    player.speed(0)  # 0 is the maximum speed
    player.setposition(0, -270)
    player.setheading(90)  # setheading is for changing the direction of pointer to top as defalt is left

    playerspeed = 15

    # creating enemies
    # choose a number of enemies
    number_of_enemies = 5
    # create an empty list of enemies
    enemies = []

    # add enemies to the list
    for i in range(number_of_enemies):
        # create the enemy
        enemies.append(turtle.Turtle())

    for enemy in enemies:
        enemy.color("blue")
        enemy.shape("circle")
        enemy.penup()
        enemy.speed(0)
        x = random.randint(-200, 200)
        y = random.randint(100, 250)
        enemy.setposition(x, y)

    enemyspeed = 2

    # creating player bullet
    bullet = turtle.Turtle()
    bullet.color("yellow")
    bullet.shape("triangle")
    bullet.penup()  # so that it does not make line or we don't want that bullet draw line
    bullet.speed(0)
    bullet.setheading(90)
    bullet.shapesize(0.4, 0.4)
    bullet.hideturtle()

    bulletspeed = 40

    # moving player left ant right
    def move_left():
        x = player.xcor()  # this command is for current position of player
        x -= playerspeed  # this command tracks the players position or subtract current position and playerspeed
        if x < -280:  # this command is for making player bounded to a fixed area or we can say it is a boundary checking condition
            x = -280
        player.setx(x)  # this command change players current location to x

    def move_right():
        x = player.xcor()
        x += playerspeed
        if x > 280:
            x = 280
        player.setx(x)

    def fire_bullet():
        # declare bulletstate as a global if it needs changed
        # defining bullet state
        # ready - ready to fire
        # fire - bullet is firing
        bulletstate = "ready"
        if bulletstate == "ready":  # for cheaking that bullet stae is ready or has been fired
            bulletstate = "fire"
            # move the bullet to the just above player/enemy
            x = player.xcor()
            y = player.ycor() + 10
            bullet.setposition(x, y)
            bullet.showturtle()

    def iscollision(t1, t2):
        distance = math.sqrt(math.pow(t1.xcor() - t2.xcor(), 2) + math.pow(t1.ycor() - t2.ycor(), 2))
        if distance < 15:
            return True
        else:
            return False

    # creating keyboard bindings
    turtle.listen()
    turtle.onkey(move_left, "Left")  # it makes player move left
    turtle.onkey(move_right, "Right")  # it makes player move right
    turtle.onkey(fire_bullet, "space")

    # main game loop
    while True:

        for enemy in enemies:
            # for making enemy move
            x = enemy.xcor()
            x += enemyspeed
            enemy.setx(x)

            # move enemy back and down
            if enemy.xcor() > 280:  # so that it does not goes beyond a fixed area or for boundary checking
                # making all enemies move down
                for e in enemies:  # so that all enemies move down
                    y = e.ycor()  # for  moving enemy down
                    y -= 40
                    e.sety(y)
                # For changing direction of enemies
                enemyspeed *= -1  # so that it can rebound with more speed

            if enemy.xcor() < -280:
                # making all enemies move down
                for e in enemies:
                    y = e.ycor()
                    y -= 40
                    e.sety(y)
                # For changing direction of enemies
                enemyspeed *= -1

            # check for a collision between the bullet and the enemy
            if iscollision(bullet, enemy):
                # resrt the bullet
                bullet.hideturtle()
                bulletstate = "ready"
                bullet.setposition(0, -400)
                # reset the enemy
                x = random.randint(-200, 200)
                y = random.randint(100, 250)
                enemy.setposition(x, y)
                # updating the score
                score += 10
                scorestring = "score %s" % score
                score_pen.clear()
                score_pen.write(scorestring, False, align="left", font=("Arial", 15, "normal"))

            if iscollision(player, enemy):
                player.hideturtle()
                print "Game Over"
                break

        # moveing the bullet
        y = bullet.ycor()
        y += bulletspeed
        bullet.sety(y)

        # checking to see if bullet has gone to top
        if bullet.ycor() > 275:
            bullet.hideturtle()
            bulletstate = "ready"

    delay = raw_input("press F to finish")

def tetris():
    # The configuration
    cell_size = 18
    cols = 10
    rows = 22
    maxfps = 120

    colors = [
        (0, 0, 0),
        (255, 85, 85),
        (100, 200, 115),
        (120, 108, 245),
        (255, 140, 50),
        (50, 120, 52),
        (146, 202, 73),
        (150, 161, 218),
        (35, 35, 35)  # Helper color for background grid
    ]

    # Define the shapes of the single parts
    tetris_shapes = [
        [[1, 1, 1],
         [0, 1, 0]],

        [[0, 2, 2],
         [2, 2, 0]],

        [[3, 3, 0],
         [0, 3, 3]],

        [[4, 0, 0],
         [4, 4, 4]],

        [[0, 0, 5],
         [5, 5, 5]],

        [[6, 6, 6, 6]],

        [[7, 7],
         [7, 7]]
    ]

    def rotate_clockwise(shape):
        return [[shape[y][x]
                 for y in xrange(len(shape))]
                for x in xrange(len(shape[0]) - 1, -1, -1)]

    def check_collision(board, shape, offset):
        off_x, off_y = offset
        for cy, row in enumerate(shape):
            for cx, cell in enumerate(row):
                try:
                    if cell and board[cy + off_y][cx + off_x]:
                        return True
                except IndexError:
                    return True
        return False

    def remove_row(board, row):
        del board[row]
        return [[0 for i in xrange(cols)]] + board

    def join_matrixes(mat1, mat2, mat2_off):
        off_x, off_y = mat2_off
        for cy, row in enumerate(mat2):
            for cx, val in enumerate(row):
                mat1[cy + off_y - 1][cx + off_x] += val
        return mat1

    def new_board():
        board = [[0 for x in xrange(cols)]
                 for y in xrange(rows)]
        board += [[1 for x in xrange(cols)]]
        return board

    class TetrisApp(object):
        def __init__(self):
            pygame.init()
            pygame.key.set_repeat(250, 25)
            self.width = cell_size * (cols + 6)
            self.height = cell_size * rows
            self.rlim = cell_size * cols
            self.bground_grid = [[8 if x % 2 == y % 2 else 0 for x in xrange(cols)] for y in xrange(rows)]

            self.default_font = pygame.font.Font(
                pygame.font.get_default_font(), 12)

            self.screen = pygame.display.set_mode((self.width, self.height))
            pygame.event.set_blocked(pygame.MOUSEMOTION)  # We do not need
            # mouse movement
            # events, so we
            # block them.
            self.next_stone = tetris_shapes[rand(len(tetris_shapes))]
            self.init_game()

        def new_stone(self):
            self.stone = self.next_stone[:]
            self.next_stone = tetris_shapes[rand(len(tetris_shapes))]
            self.stone_x = int(cols / 2 - len(self.stone[0]) / 2)
            self.stone_y = 0

            if check_collision(self.board,
                               self.stone,
                               (self.stone_x, self.stone_y)):
                self.gameover = True

        def init_game(self):
            self.board = new_board()
            self.new_stone()
            self.level = 1
            self.score = 0
            self.lines = 0
            pygame.time.set_timer(pygame.USEREVENT + 1, 1000)

        def disp_msg(self, msg, topleft):
            x, y = topleft
            for line in msg.splitlines():
                self.screen.blit(
                    self.default_font.render(
                        line,
                        False,
                        (255, 255, 255),
                        (0, 0, 0)),
                    (x, y))
                y += 14

        def center_msg(self, msg):
            for i, line in enumerate(msg.splitlines()):
                msg_image = self.default_font.render(line, False,
                                                     (255, 255, 255), (0, 0, 0))

                msgim_center_x, msgim_center_y = msg_image.get_size()
                msgim_center_x //= 2
                msgim_center_y //= 2

                self.screen.blit(msg_image, (
                    self.width // 2 - msgim_center_x,
                    self.height // 2 - msgim_center_y + i * 22))

        def draw_matrix(self, matrix, offset):
            off_x, off_y = offset
            for y, row in enumerate(matrix):
                for x, val in enumerate(row):
                    if val:
                        pygame.draw.rect(
                            self.screen,
                            colors[val],
                            pygame.Rect(
                                (off_x + x) *
                                cell_size,
                                (off_y + y) *
                                cell_size,
                                cell_size,
                                cell_size), 0)

        def add_cl_lines(self, n):
            linescores = [0, 40, 100, 300, 1200]
            self.lines += n
            self.score += linescores[n] * self.level
            if self.lines >= self.level * 6:
                self.level += 1
                newdelay = 1000 - 50 * (self.level - 1)
                newdelay = 100 if newdelay < 100 else newdelay
                pygame.time.set_timer(pygame.USEREVENT + 1, newdelay)

        def move(self, delta_x):
            if not self.gameover and not self.paused:
                new_x = self.stone_x + delta_x
                if new_x < 0:
                    new_x = 0
                if new_x > cols - len(self.stone[0]):
                    new_x = cols - len(self.stone[0])
                if not check_collision(self.board,
                                       self.stone,
                                       (new_x, self.stone_y)):
                    self.stone_x = new_x

        def quit(self):
            self.center_msg("Exiting...")
            pygame.display.update()
            sys.exit()

        def drop(self, manual):
            if not self.gameover and not self.paused:
                self.score += 1 if manual else 0
                self.stone_y += 1
                if check_collision(self.board,
                                   self.stone,
                                   (self.stone_x, self.stone_y)):
                    self.board = join_matrixes(
                        self.board,
                        self.stone,
                        (self.stone_x, self.stone_y))
                    self.new_stone()
                    cleared_rows = 0
                    while True:
                        for i, row in enumerate(self.board[:-1]):
                            if 0 not in row:
                                self.board = remove_row(
                                    self.board, i)
                                cleared_rows += 1
                                break
                        else:
                            break
                    self.add_cl_lines(cleared_rows)
                    return True
            return False

        def insta_drop(self):
            if not self.gameover and not self.paused:
                while (not self.drop(True)):
                    pass

        def rotate_stone(self):
            if not self.gameover and not self.paused:
                new_stone = rotate_clockwise(self.stone)
                if not check_collision(self.board,
                                       new_stone,
                                       (self.stone_x, self.stone_y)):
                    self.stone = new_stone

        def toggle_pause(self):
            self.paused = not self.paused

        def start_game(self):
            if self.gameover:
                self.init_game()
                self.gameover = False

        def run(self):
            key_actions = {
                'ESCAPE': self.quit,
                'LEFT': lambda: self.move(-1),
                'RIGHT': lambda: self.move(+1),
                'DOWN': lambda: self.drop(True),
                'UP': self.rotate_stone,
                'p': self.toggle_pause,
                'SPACE': self.start_game,
                'RETURN': self.insta_drop
            }

            self.gameover = False
            self.paused = False

            dont_burn_my_cpu = pygame.time.Clock()
            while 1:
                self.screen.fill((0, 0, 0))
                if self.gameover:
                    self.center_msg("""Game Over!\nYour score: %d
    Press space to continue""" % self.score)
                else:
                    if self.paused:
                        self.center_msg("Paused")
                    else:
                        pygame.draw.line(self.screen,
                                         (255, 255, 255),
                                         (self.rlim + 1, 0),
                                         (self.rlim + 1, self.height - 1))
                        self.disp_msg("Next:", (
                            self.rlim + cell_size,
                            2))
                        self.disp_msg("Score: %d\n\nLevel: %d\
    \nLines: %d" % (self.score, self.level, self.lines),
                                      (self.rlim + cell_size, cell_size * 5))
                        self.draw_matrix(self.bground_grid, (0, 0))
                        self.draw_matrix(self.board, (0, 0))
                        self.draw_matrix(self.stone,
                                         (self.stone_x, self.stone_y))
                        self.draw_matrix(self.next_stone,
                                         (cols + 1, 2))
                pygame.display.update()

                for event in pygame.event.get():
                    if event.type == pygame.USEREVENT + 1:
                        self.drop(False)
                    elif event.type == pygame.QUIT:
                        self.quit()
                    elif event.type == pygame.KEYDOWN:
                        for key in key_actions:
                            if event.key == eval("pygame.K_"
                                                         + key):
                                key_actions[key]()

                dont_burn_my_cpu.tick(maxfps)

    if __name__ == '__main__':
        App = TetrisApp()
        App.run()

def main():
    master = Tkinter.Tk()
    master.geometry("%dx%d+0+0" % (500, 500))
    label = Tkinter.Label(master, text=" GAMES ", fg="red", font=("Arial", 50)).pack(side=Tkinter.TOP, expand=Tkinter.YES, fill=Tkinter.X)
    b = Tkinter.Button(master, text="Play  Space  game", command=CallBack).pack(side=Tkinter.TOP, expand=Tkinter.YES, fill=Tkinter.X)
    d = Tkinter.Button(master, text="Play  Tetris", command=tetris).pack(side=Tkinter.TOP, expand=Tkinter.YES, fill=Tkinter.X)
    a = Tkinter.Button(master, text="Quit", command=quit).pack(side=Tkinter.BOTTOM, expand=Tkinter.YES, fill=Tkinter.X)
    c = Tkinter.Button(master, text="setting", command=setting).pack(side=Tkinter.TOP, expand=Tkinter.YES, fill=Tkinter.X)

    Tkinter.mainloop()

main()
