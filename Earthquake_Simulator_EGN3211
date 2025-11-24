#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define WIDTH 20
#define HEIGHT 20

// color codes
#define RED     "\033[31m"
#define GREEN   "\033[32m"
#define YELLOW  "\033[33m"
#define RESET   "\033[0m"

// Clear screen 
void clear_screen(void) {
	printf("\033[2J\033[H");
}

// Shape 1 vertical line at x
void shape_one(int x_ch, int fault_line[HEIGHT][WIDTH], int stress_level[HEIGHT][WIDTH]) {
	if (x_ch < 0 || x_ch >= WIDTH) return;  // simple bounds check

	for (int i = 0; i < HEIGHT; i++) {
		fault_line[i][x_ch] = 1;   // mark fault
	}
}

// Shape 2 horizontal line at y
void shape_two(int y_ch, int fault_line[HEIGHT][WIDTH], int stress_level[HEIGHT][WIDTH]) {
	if (y_ch < 0 || y_ch >= HEIGHT) return; // simple bounds check

	for (int j = 0; j < WIDTH; j++) {
		fault_line[y_ch][j] = 1;   // mark fault
	}
}

// Shape 3 circular with a center at (x,y)
void shape_three(int x_ch, int y_ch,
                 int fault_line[HEIGHT][WIDTH],
                 int stress_level[HEIGHT][WIDTH]) {

	// list of the "circle" points (center + radius 5 pattern)
	int pts[][2] = {

		{x_ch-5, y_ch},      // axis points (radius 5)
		{x_ch+5, y_ch},
		{x_ch,   y_ch-5},
		{x_ch,   y_ch+5},

		{x_ch-4, y_ch-1},   
		{x_ch-4, y_ch+1},
		{x_ch+4, y_ch-1},
		{x_ch+4, y_ch+1},
		{x_ch-1, y_ch-4},
		{x_ch+1, y_ch-4},
		{x_ch-1, y_ch+4},
		{x_ch+1, y_ch+4},

		{x_ch-4, y_ch-2},    
		{x_ch-4, y_ch+2},
		{x_ch+4, y_ch-2},
		{x_ch+4, y_ch+2},
		{x_ch-2, y_ch-4},
		{x_ch+2, y_ch-4},
		{x_ch-2, y_ch+4},
		{x_ch+2, y_ch+4},

		{x_ch-3, y_ch-3},    
		{x_ch-3, y_ch+3},
		{x_ch+3, y_ch-3},
		{x_ch+3, y_ch+3},

		{x_ch-5, y_ch-1},    
		{x_ch-5, y_ch+1},
		{x_ch+5, y_ch-1},
		{x_ch+5, y_ch+1},
		{x_ch-1, y_ch-5},
		{x_ch+1, y_ch-5},
		{x_ch-1, y_ch+5},
		{x_ch+1, y_ch+5}
	};

	int n = sizeof(pts) / sizeof(pts[0]);

	for (int i = 0; i < n; i++) {
		int x = pts[i][0];
		int y = pts[i][1];
		if (x >= 0 && x < HEIGHT && y >= 0 && y < WIDTH) {
			fault_line[x][y] = 1; // mark fault
		}
	}
}

// Shape 4 straight line from (x1,y1) to (x2,y2)
void shape_four(int x_ch_one, int y_ch_one,
                int x_ch_two, int y_ch_two,
                int fault_line[HEIGHT][WIDTH],
                int stress_level[HEIGHT][WIDTH]) {

	int x1 = x_ch_one;
	int y1 = y_ch_one;
	int x2 = x_ch_two;
	int y2 = y_ch_two;

	int dx = abs(x2 - x1);
	int sx = (x1 < x2) ? 1 : -1;

	int dy = -abs(y2 - y1);
	int sy = (y1 < y2) ? 1 : -1;

	int err = dx + dy;   // error term
	int x = x1;
	int y = y1;

	while (1) {
		// bounds check
		if (x >= 0 && x < HEIGHT && y >= 0 && y < WIDTH) {
			fault_line[x][y] = 1; // mark fault
		}

		if (x == x2 && y == y2) {
			break;
		}

		int e2 = 2 * err;

		if (e2 >= dy) {  // step in x
			err += dy;
			x += sx;
		}
		if (e2 <= dx) {  // step in y
			err += dx;
			y += sy;
		}
	}
}

// Update stress
void update_stress_levels(int fault_line[HEIGHT][WIDTH],
                          int stress_level[HEIGHT][WIDTH]) {

	for (int i = 0; i < HEIGHT; i++) {
		for (int j = 0; j < WIDTH; j++) {
			int delta;

			if (fault_line[i][j] == 1) {
				// on a fault line: add random [0, 10]
				delta = rand() % 11;       // 0..10
			} else {
				// not on a fault line: add random [-5, +5]
				delta = (rand() % 11) - 5; // -5..5
			}

			stress_level[i][j] += delta;

			// stress must never be negative
			if (stress_level[i][j] < 0) {
				stress_level[i][j] = 0;
			}
		}
	}
}

// Print fault lines in red
void print_fault_line(int fault_line[HEIGHT][WIDTH]) {
	printf("------------------------\n");
	printf(" Printing the Fault Map\n");
	printf("------------------------\n");

	for (int i = 0; i < HEIGHT; i++) {
		for (int j = 0; j < WIDTH; j++) {
			if (fault_line[i][j] == 1) {
				printf(RED "1 " RESET);
			} else {
				printf("0 ");
			}
		}
		printf("\n");
	}
}

// Print stress map with color coding
void print_stress_map(int stress_level[HEIGHT][WIDTH]) {
	printf("------------------------\n");
	printf(" Printing the Surface\n");
	printf("------------------------\n");

	for (int i = 0; i < HEIGHT; i++) {
		for (int j = 0; j < WIDTH; j++) {
			int v = stress_level[i][j];
			const char *color;

			if (v < 50) {
				color = GREEN;   // low
			} else if (v < 100) {
				color = YELLOW;  // medium
			} else {
				color = RED;     // high
			}

			printf("%s%3d%s ", color, v, RESET);
		}
		printf("\n");
	}
}

// Main
int main() {
	printf("                ********************\n");
	printf("                EARTHQUAKE SIMULATOR\n");
	printf("                ********************\n\n");

	int stress_level[HEIGHT][WIDTH] = {0};
	int fault_line[HEIGHT][WIDTH]   = {0};

	int x_chord, y_chord;
	int x_chord_two, y_chord_two;
	int choice;

	// seed RNG once
	srand((unsigned int)time(NULL));

    // Fault lines
	while (1) {
		printf("Please choose a fault line shape to generate...\n");
		printf("1: Vertical Line\n");
		printf("2: Horizontal Line\n");
		printf("3: Circle\n");
		printf("4: Point to Point Line\n");
		printf("-1: Begin Simulation\n");
		printf("ENTER: ");
		scanf("%d", &choice);  // FIXED: Moved scanf before the check

		if (choice == -1) {
			break;  // move on to simulation
		}

		if (choice == 1) {
			printf("At what X location would you like to place this vertical line? (0-%d)\n", WIDTH - 1);
			printf("ENTER: ");
			scanf("%d", &x_chord);
			shape_one(x_chord, fault_line, stress_level);
		}
		else if (choice == 2) {
			printf("At what Y location would you like to place this horizontal line? (0-%d)\n", HEIGHT - 1);
			printf("ENTER: ");
			scanf("%d", &y_chord);
			shape_two(y_chord, fault_line, stress_level);
		}
		else if (choice == 3) {
			printf("At what X,Y location would you like to place this circle? (0-%d), (0-%d)\n",
			       WIDTH - 1, HEIGHT - 1);
			printf("ENTER (format: x,y): ");
			scanf("%d,%d", &x_chord, &y_chord);
			shape_three(x_chord, y_chord, fault_line, stress_level);
		}
		else if (choice == 4) {
			printf("Enter X,Y of start point (0-%d), (0-%d) (format: x,y): ", WIDTH - 1, HEIGHT - 1);
			scanf("%d,%d", &x_chord, &y_chord);

			printf("Enter X,Y of end point (0-%d), (0-%d) (format: x,y): ", WIDTH - 1, HEIGHT - 1);
			scanf("%d,%d", &x_chord_two, &y_chord_two);

			shape_four(x_chord, y_chord, x_chord_two, y_chord_two,
			           fault_line, stress_level);
		}
		else {
			printf("Invalid choice, try again.\n");
		}

		// Clear 
		clear_screen();
		print_fault_line(fault_line);
	}

	// Full simulation
	printf("\nBeginning simulation...\n\n");

	int step = 0;
	int earthquake = 0;

	while (!earthquake) {
		clear_screen();

		printf("Simulation step: %d\n\n", step++);
		update_stress_levels(fault_line, stress_level);

		print_stress_map(stress_level);

		// check for earthquake (>= 200)
		earthquake = 0;
		for (int i = 0; i < HEIGHT; i++) {
			for (int j = 0; j < WIDTH; j++) {
				if (stress_level[i][j] >= 200) {
					earthquake = 1;
				}
			}
		}

		if (earthquake) {
    		printf("\n%s----------------------------------------------------------------------------------%s\n", RED, RESET);
    	    printf("\n%s                                EARTHQUAKE!! %s\n", RED, RESET);
        	printf("\n%s----------------------------------------------------------------------------------%s\n", RED, RESET);
		}

		// Add small delay to see simulation steps
		#ifdef _WIN32
		system("timeout /t 1 >nul");
		#else
		system("sleep 0.5");
		#endif
	}

	return 0;
}
