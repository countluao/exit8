#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef char* string;
typedef enum
{
    false = 0, true = 1
}bool;

#define MAX_ROWS 17
#define MAX_COLS 20
#define LEVEL_COUNT 8

char levels[LEVEL_COUNT][MAX_ROWS][MAX_COLS + 1] = 
{
    // Easy
    {
        "############",
        "#@   #     #",
        "# # # ### ##",
        "# #   #   E#",
        "# ##### ####",
        "#          #",
        "############"
    },
    // Medium
    {
        "############",
        "#@       #E#",
        "# #####  L #",
        "#     #    #",
        "#### ####L #",
        "#          #",
        "############"
    },
    // Hard (updated)
    {
        "############",
        "#@   E#E  E#",
        "# #####E #E#",
        "#  #E#     #",
        "#  # #   # #",
        "#        #E#",
        "############"
    },
    // Impossible
    {
        "########### ",
        "#@       ## ",
        "# #### ##   ",
        "#  #E#    # ",
        "# ## ###### ",
        "#  #        ",
        "########### "
    },
    // Extension
    {
        "############",
        "#@  #   #  #",
        "# ##### #  #",
        "#       #  #",
        "# #######  #",
        "#E#######  #",
        "##         #",
        "#########  #",
        "#E#        #",
        "############"
    },
    //portal
    {
        "#############",
        "#@ A   E#   #",
        "#### ###### #",
        "#    #      #",
        "######C#### #",
        "#D     #   ##",
        "######## ####",
        "#  B#G      #",
        "#####F#     #",
        "#############"
    },
    //key
    {
        "#############",
        "#@    D    E#",
        "#     #L#####",
        "#       k   #",
        "# L       L #",
        "#      L    #",
        "#           #",
        "#############"
    },
    //two players
    {
        "#############",
        "#@         E#",
        "# ### ##### #",
        "# #   #   # #",
        "# # # # # # #",
        "#   #   #   #",
        "#############",
        "#$###     #E#",
        "# ### ##### #",
        "#         # #",
        "# # # # # # #",
        "#           #",
        "#############"
    }
};

bool is_blocked(char maze[MAX_ROWS][MAX_COLS + 1], int level, int to_row, int to_col, int to_row2, int to_col2, bool triggered, bool got_key) {
    if (maze[to_row][to_col] == '#') return true;
    if (maze[to_row][to_col] == 'L') return true;

    // Medium level: laser trap
    if (level == 2) 
    {
        if (to_col == 9 && to_row == 3)
        {
            return true;
        }
    }

    // Hard level: block all 'E' except the correct one at (5,11)
    if (level == 3 && maze[to_row][to_col] == 'E' && !(to_row == 5 && to_col == 10)) 
    {
        return true;
    }

    if (level == 5 && to_row == 5 && to_col == 1)
    {
        return true;
    }

    // Extension level: gate logic
    if (level == 5 && to_row == 8 && to_col == 2 && !triggered) {
        return true; // wall gate still closed
    }
    //key level: lazer trap 
    if (level == 7 && ((to_col == 7 && (to_row == 3 || to_row == 4)) || (to_row == 4 && (to_col == 3 || to_col == 4 || to_col == 5 || to_col == 6 || to_col == 7 || to_col == 8 || to_col == 9))))
    {
        return true;
    }
    if (level == 7 && to_row == 1 && to_col == 6 && !got_key)
    {
        return true;
    }
    if (level == 8 && maze[to_row2][to_col2] == '#')
    {
        return true;
    }
    return false;
}

void load_maze(char maze[MAX_ROWS][MAX_COLS + 1], int level) 
{
    for (int i = 0; i < MAX_ROWS; i++) 
    {
        strcpy(maze[i], levels[level - 1][i]);
    }
}

void print_maze(char maze[MAX_ROWS][MAX_COLS + 1]) 
{
    for (int i = 0; i < MAX_ROWS; i++) 
    {
        printf("%s\n", maze[i]);
    }
}

int main() 
{
    bool got_key = false;
    bool c_place = true;
    bool a_place = true;
    bool b_place = true;
    bool d_place = true;
    bool f_place = true;
    bool g_place = true;
    int level;
    printf("\n\nForward:\nInspired by the psychological horror game The Exit 8, where you must escape an endless corridor by not ignoring anomalies.");
    printf("\n\nRules:\nthey will appear as you play all the levels");
    printf("\n\nChoose level (1-Easy, 2-Medium, 3-Hard, 4-Impossible, 5-Extension, 6-Portal, 7-key & door, 8-two you's): ");
    scanf("%d", &level);
    if (level < 1 || level > LEVEL_COUNT) 
    {
        printf("Invalid level.\n");
        return 1;
    }

    char maze[MAX_ROWS][MAX_COLS + 1];
    load_maze(maze, level);

    int player_row = 1, player_col = 1;
    int player_row2 = 7, player_col2 = 1;
    bool triggered = false;
    printf("a clue for this level:\n");
    if (level == 1)
    {
        printf("There is a path to the E\n");
    }
    else if (level == 2)
    {
        printf("There is only one safe route\n");
    }
    else if (level == 3)
    {
        printf("Not all E\'s are exits\n");
    }
    else if (level == 4)
    {
        printf("Think outside the box...");
    }
    else if (level == 5)
    {
        printf("There are triggers...");
    }
    else if (level == 6)
    {
        printf("The exit is like, right there bro");
    }
    else if (level == 7)
    {
        printf("Just get the key bro");
    }
    else if (level == 8)
    {
        printf("Look on both sides");
    }
    getchar();getchar();

    while (true) 
    {
        print_maze(maze);
        printf("Move (WASD): ");
        char move;
        scanf(" %c", &move);

        int new_row = player_row;
        int new_col = player_col;
        int new_row2 = player_row2;
        int new_col2 = player_col2;
        if (move == 'w' || move == 'W')
        {
            new_row--;
            new_row2--;
        } 
        else if (move == 's' || move == 'S')
        {
            new_row++;
            new_row2++;
        } 
        else if (move == 'a' || move == 'A')
        {
            new_col--;
            new_col2--;
        } 
        else if (move == 'd' || move == 'D')
        {
            new_col++;
            new_col2++;
        } 
        else continue;

        if (new_row < 0 || new_row >= MAX_ROWS || new_col < 0 || new_col >= MAX_COLS) continue;

        if (!is_blocked(maze, level, new_row, new_col, new_row2, new_col2, triggered, got_key)) 
        {
            
            if ((level == 1 && new_row == 3 && new_col == 10) ||
                (level == 2 && new_row == 1 && new_col == 10) ||
                (level == 3 && new_row == 5 && new_col == 11) ||
                (level == 4 && new_row == 3 && new_col == 4) ||
                (level == 5 && new_row == 8 && new_col == 1)) 
            {
                maze[player_row][player_col] = ' ';
                maze[new_row][new_col] = '@';
                print_maze(maze);
                printf("You reached the exit!\n");
                break;
            }

            maze[player_row][player_col] = ' ';
            maze[new_row][new_col] = '@';
            if (level == 8)
            {
                maze[player_row2][player_col2] = ' ';
                maze[new_row2][new_col2] = '$';
            }
            player_row = new_row;
            player_col = new_col;
            player_row2 = new_row2;
            player_col2 = new_col2;
            if (level == 5 && new_row == 5 && new_col == 1)
            {

            }
            if (level == 5 && player_row == 1 && player_col == 7)
            {
                printf("this wall feels strage...\n");
                getchar();getchar();
            }
            if (level == 5 && player_row == 6 && player_col == 2)
            {
                printf("i think i heard a ckick...\n");
                maze[8][2] = ' ';
                getchar();getchar();
            }
            if (level == 3 && player_row == 5 && player_col == 10)
            {
                maze[player_row][player_col] = ' ';
                maze[new_row][new_col] = '@';
                print_maze(maze);
                printf("You reached the exit!\n");
                break;
            }
            if (level == 6)
            {
                if (player_row == 1 && player_col == 3 && a_place)
                {
                    new_row = 1;
                    new_col = 9;
                    maze[player_row][player_col] = ' ';
                    maze[new_row][new_col] = '@';
                    player_row = new_row;
                    player_col = new_col;
                    a_place = false;
                }
                if (player_row == 4 && player_col == 6 && c_place)
                {
                    new_row = 4;
                    new_col = 11;
                    maze[player_row][player_col] = ' ';
                    maze[new_row][new_col] = '@';
                    player_row = new_row;
                    player_col = new_col;
                    c_place = false;
                }
                if (player_row == 5 && player_col == 1 && d_place)
                {
                    new_row = 5;
                    new_col = 10;
                    maze[player_row][player_col] = ' ';
                    maze[new_row][new_col] = '@';
                    player_row = new_row;
                    player_col = new_col;
                    d_place = false;
                }
                if (player_row == 7 && player_col == 5 && g_place)
                {
                    new_row = 8;
                    new_col = 11;
                    maze[player_row][player_col] = ' ';
                    maze[new_row][new_col] = '@';
                    player_row = new_row;
                    player_col = new_col;
                    g_place = false;
                }
                if (player_row == 8 && player_col == 5 && f_place)
                {
                    new_row = 7;
                    new_col = 1;
                    maze[player_row][player_col] = ' ';
                    maze[new_row][new_col] = '@';
                    player_row = new_row;
                    player_col = new_col;
                    f_place = false;
                }
                if (player_row == 7 && player_col == 3 && b_place)
                {
                    new_row = 3;
                    new_col = 1;
                    maze[player_row][player_col] = ' ';
                    maze[new_row][new_col] = '@';
                    player_row = new_row;
                    player_col = new_col;
                    b_place = false;
                }
                if (player_row == 1 && player_col == 7)
                {
                    maze[player_row][player_col] = ' ';
                    maze[new_row][new_col] = '@';
                    print_maze(maze);
                    printf("You reached the exit!\n");
                    break;
                }
            }
            if (level == 7 && player_row == 3 && player_col == 8)
            {
                got_key = true;
                printf("You got the key! You can now open the door");
                getchar();getchar();
            }
            if (level == 7 && player_row == 1 && player_col == 11)
            {
                maze[player_row][player_col] = ' ';
                maze[new_row][new_col] = '@';
                print_maze(maze);
                printf("You reached the exit!\n");
                break;
            }
            if (level == 8 && player_row == 1 && player_col == 11)
            {
                maze[player_row][player_col] = ' ';
                maze[new_row][new_col] = '@';
                maze[player_row2][player_col2] = ' ';
                maze[new_row2][new_col2] = '$';
                print_maze(maze);
                printf("You reached the exit!\n");
                break;
            }
            
            
            
        } 
        else 
        {
            if (level == 5 && new_row == 1 && new_col == 8) 
            {
                triggered = true;
                maze[1][8] = ' '; // open wall gate
            }
            else if ((level == 2 && new_row == 3 && new_col == 9) || (level == 7 && ((new_col == 7 && (new_row == 3 || new_row == 4)) || (new_row == 4 && (new_col == 3 || new_col == 4 || new_col == 5 || new_col == 6 || new_col == 7 || new_col == 8)))))
            {
                printf("Lazers blocked you! Try again\n");
                getchar();getchar();
            }
            else if (level == 7 && new_row == 1 && new_col == 6)
            {
                printf("Door closed");
                getchar();getchar();
            }
            else
            {
                printf("Blocked! Try again.\n");
                getchar();getchar();
            }
            
        }
    }

    return 0;
}
