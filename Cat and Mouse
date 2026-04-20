from collections import deque
from typing import List

# Game constants
HOLE, MOUSE_START, CAT_START = 0, 1, 2
MOUSE_TURN, CAT_TURN = 0, 1
MOUSE_WIN, CAT_WIN, TIE = 1, 2, 0


class Solution:
    def catMouseGame(self, graph: List[List[int]]) -> int:
        """
        Determines the outcome of the cat and mouse game.
      
        Args:
            graph: Adjacency list representation of the game board
          
        Returns:
            0 if tie, 1 if mouse wins, 2 if cat wins
        """
      
        def get_previous_states(current_state: tuple) -> List[tuple]:
            """
            Get all possible previous states that could lead to current state.
          
            Args:
                current_state: (mouse_pos, cat_pos, turn)
              
            Returns:
                List of previous states (mouse_pos, cat_pos, turn)
            """
            mouse_pos, cat_pos, turn = current_state
            previous_turn = turn ^ 1  # Toggle turn using XOR
            previous_states = []
          
            if previous_turn == CAT_TURN:
                # If it was cat's turn, cat could have moved from any adjacent position
                for previous_cat_pos in graph[cat_pos]:
                    if previous_cat_pos != HOLE:  # Cat cannot be at hole
                        previous_states.append((mouse_pos, previous_cat_pos, previous_turn))
            else:
                # If it was mouse's turn, mouse could have moved from any adjacent position
                for previous_mouse_pos in graph[mouse_pos]:
                    previous_states.append((previous_mouse_pos, cat_pos, previous_turn))
          
            return previous_states
      
        n = len(graph)
      
        # Initialize result array: result[mouse][cat][turn] = game outcome
        result = [[[0, 0] for _ in range(n)] for _ in range(n)]
      
        # Initialize degree array: tracks number of possible moves from each state
        degree = [[[0, 0] for _ in range(n)] for _ in range(n)]
      
        # Calculate degrees for each state
        for mouse_pos in range(n):
            for cat_pos in range(1, n):  # Cat cannot be at position 0 (hole)
                degree[mouse_pos][cat_pos][MOUSE_TURN] = len(graph[mouse_pos])
                degree[mouse_pos][cat_pos][CAT_TURN] = len(graph[cat_pos])
          
            # Adjust cat's degree since it cannot move to hole
            for adjacent_to_hole in graph[HOLE]:
                degree[mouse_pos][adjacent_to_hole][CAT_TURN] -= 1
      
        # BFS queue for processing states
        queue = deque()
      
        # Initialize known winning states: mouse at hole
        for cat_pos in range(1, n):
            result[0][cat_pos][MOUSE_TURN] = MOUSE_WIN
            result[0][cat_pos][CAT_TURN] = MOUSE_WIN
            queue.append((0, cat_pos, MOUSE_TURN))
            queue.append((0, cat_pos, CAT_TURN))
      
        # Initialize known winning states: cat catches mouse
        for pos in range(1, n):
            result[pos][pos][MOUSE_TURN] = CAT_WIN
            result[pos][pos][CAT_TURN] = CAT_WIN
            queue.append((pos, pos, MOUSE_TURN))
            queue.append((pos, pos, CAT_TURN))
      
        # Process states using BFS
        while queue:
            current_state = queue.popleft()
            current_mouse, current_cat, current_turn = current_state
            current_result = result[current_mouse][current_cat][current_turn]
          
            # Check all states that could lead to current state
            for previous_state in get_previous_states(current_state):
                prev_mouse, prev_cat, prev_turn = previous_state
              
                # Skip if this previous state already has a determined outcome
                if result[prev_mouse][prev_cat][prev_turn] != TIE:
                    continue
              
                # Check if previous player can force a win
                is_winning_move = (
                    (current_result == MOUSE_WIN and prev_turn == MOUSE_TURN) or
                    (current_result == CAT_WIN and prev_turn == CAT_TURN)
                )
              
                if is_winning_move:
                    # Previous player found a winning move
                    result[prev_mouse][prev_cat][prev_turn] = current_result
                    queue.append(previous_state)
                else:
                    # Decrease the degree (number of unexplored moves)
                    degree[prev_mouse][prev_cat][prev_turn] -= 1
                  
                    # If all moves lead to loss, previous player loses
                    if degree[prev_mouse][prev_cat][prev_turn] == 0:
                        result[prev_mouse][prev_cat][prev_turn] = current_result
                        queue.append(previous_state)
      
        # Return the result for initial state
        return result[MOUSE_START][CAT_START][MOUSE_TURN]
