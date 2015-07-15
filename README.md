# main.c
Main source file of Battleship

/*********************************************
* Project: Battleship                        *
* Author: Marina Ljuboja                     *
*                                            *
* Description: User plays the computer in a  *
* game of battleship. Each player has a 10 x *
* 10 board with 5 ships. The first to sink   *
* all of the opponents ships wins. Each move *
* and stats will be collected in a separate  *
* file.                                      *
*                                            *
**********************************************/

#include "battleship.h"

int main (void)
{
	char p1_gameboard[MAX_ROWS][MAX_COLS] = {{'~','~'},{'~'}}, p2_gameboard[MAX_ROWS][MAX_COLS] = {{'~','~'},{'~'}};

	int rows = 10, cols = 10, placement_type = 0, first = 0, p1_win = 0, p2_win = 0, who_starts = 0, x1 = 0, y1 = 0,
		temp_hits = 0, temp_misses = 0, p1status[5], p2status[5];      

	player_stats p1stats, p2stats;

	FILE *output = NULL;

	p1stats.hits_to_misses = 0;
	p1stats.total_hits = 0;
	p1stats.total_misses = 0;
	p1stats.total_shots = 0;

	p2stats.hits_to_misses = 0;
	p2stats.total_hits = 0;
	p2stats.total_misses = 0;
	p2stats.total_shots = 0;
	
	srand(time(NULL));

	output = fopen("battleship.log","w");

	while ((p1_win == 0) && (p2_win == 0))
	{
		welcome_message ();

		intialize_gameboard (p1_gameboard, rows, cols);
		intialize_gameboard (p2_gameboard, rows, cols);

		p1_ship_placement (p1_gameboard);
			
		randomly_place_ships_on_board(p2_gameboard, 2, 0);

		who_starts = rand() % 2;

		if (who_starts == 0)
		{
			while ((p1_win == 0) && (p2_win == 0))
			{
				check_enemy (p2_gameboard, x1, y1, &p1stats, output);
				check_sink (p2_gameboard, 1, &p1stats, output, p1status);
				
				check_player(p1_gameboard, x1, y1, &p2stats, output);
				check_sink (p1_gameboard, 0, &p2stats, output, p2status);

				if ((p1status[0] == 1)  && (p1status[1] == 1) && (p1status[2] == 1) && (p1status[3] == 1) && (p1status[4] == 1))
				{
					p1_win = 1;
				}
				else if ((p2status[0] == 1)  && (p2status[1] == 1) && (p2status[2] == 1) && (p2status[3] == 1) && (p2status[4] == 1))
				{
					p2_win = 1;
				}
			}
		}
		else 
		{
			while ((p1_win == 0) && (p2_win == 0))
			{
				check_player(p1_gameboard, x1, y1, &p2stats, output);
				check_sink (p1_gameboard, 0, &p2stats, output, p2status);

				check_enemy(p2_gameboard, x1, y1, &p1stats, output);
				check_sink (p2_gameboard, 1, &p1stats, output, p1status);

				if ((p1status[0] == 1)  && (p1status[1] == 1) && (p1status[2] == 1) && (p1status[3] == 1) && (p1status[4] == 1))
				{
					p1_win = 1;
				}
				else if ((p2status[0] == 1) && (p2status[1] == 1) && (p2status[2] == 1) && (p2status[3] == 1) && (p2status[4] == 1))
				{
					p2_win = 1;
				}
			}
		}
	}

	if (p1_win == 1)
	{
		printf("Player 1: you win!\n\n");
		fprintf(output, "\nPlayer 1 Wins.\n");
		system("pause");
		system("cls");
	}

	else if (p2_win == 1)
	{
		printf("Player 2 wins.\n\n");
		fprintf(output, "\nPlayer 2 Wins.\n");
		system("pause");
		system("cls");
	}

	temp_hits = p1stats.total_hits;
	temp_misses = p1stats.total_misses;
	p1stats.hits_to_misses = (((double)temp_hits)/((double)temp_misses)) * 100;

	temp_hits = p2stats.total_hits;
	temp_misses = p2stats.total_misses;
	p2stats.hits_to_misses = (((double)temp_hits)/((double)temp_misses)) * 100;

	fprintf(output,"\n      PLAYER 1 STATS\n\n");
	fprintf(output,"\n  Total hits: %18d\n", p1stats.total_hits);
	fprintf(output,"\n  Total misses: %16d\n", p1stats.total_misses);
	fprintf(output,"\n  Total shots: %17d\n", p1stats.total_shots);
	fprintf(output,"\n  Hits to misses: %14d%%\n\n", p1stats.hits_to_misses);

	fprintf(output,"\n      PLAYER 2 STATS\n\n");
	fprintf(output,"\n  Total hits: %18d\n", p2stats.total_hits);
	fprintf(output,"\n  Total misses: %16d\n", p2stats.total_misses);
	fprintf(output,"\n  Total shots: %17d \n", p2stats.total_shots);
	fprintf(output,"\n  Hits to misses: %14d%%\n\n", p2stats.hits_to_misses);

	fclose(output);

	return 0;

}
