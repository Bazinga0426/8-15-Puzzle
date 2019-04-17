import javax.swing.*;
import java.util.*;

public class Puzzle {

    private final int digitSize;
    private final int rowSize;
    private State startState;
    private State endState;
    private State currentState;
    private List<State> openList;
    private List<State> closeList;


    public Puzzle(int digitSize) {
        this.digitSize = digitSize;
        this.rowSize = (int) Math.sqrt(digitSize);
        this.openList = new ArrayList<>();
        this.closeList = new ArrayList<>();
    }

    public static void main(String[] args) {
        Object selectedValue = JOptionPane.showInputDialog(null, "Choose Puzzle Scale:", "Puzzle Scale Input",
                JOptionPane.INFORMATION_MESSAGE, null, new Object[]{8, 15}, 8);
        int digitSize = Integer.valueOf(selectedValue.toString()) + 1;
        Puzzle puzzle = new Puzzle(digitSize);
        State startState;
        State endState;

        while (true) {
            String input = JOptionPane.showInputDialog(null, "Please enter the initial state", "Initial State Input", JOptionPane.PLAIN_MESSAGE);
            startState = puzzle.buildState(input);
            if (null == startState) {
                JOptionPane.showMessageDialog(null,
                        "There should be " + digitSize + " unique  numbers in range[0," + (digitSize - 1) + " separated by space." + System.lineSeparator()
                                + "Please check your input.");
                continue;
            }

            input = JOptionPane.showInputDialog(null, "Please enter the final/end state", "Final/End State Input", JOptionPane.PLAIN_MESSAGE);
            endState = puzzle.buildState(input);
            if (null == endState) {
                JOptionPane.showMessageDialog(null,
                        "There should be " + digitSize + " unique  numbers in range[0," + (digitSize - 1) + " separated by space." + System.lineSeparator()
                                + "Please check your input.");
                continue;
            }
            break;
        }
        puzzle.startState = startState;
        puzzle.endState = endState;
        puzzle.run();
    }

    //
    public void run() {
        currentState = startState;

        do {
            List<State> possibleStates = currentState.availableStates();
            //Those possible states that are not in closed are added into the open list
            for (State possibleState : possibleStates) {
                if (!closeList.contains(possibleState)) {
                    openList.add(possibleState);
                }
            }

            //Add C into closed
            closeList.add(currentState);

            //Estimate the value of f(n) for all the members of open
            for (State state : openList) {
                state.cost = cost(state);
            }

            //select the next state with min cost from open list,assign it to C
            //and add it into closed and remove it from open
            openList.sort(State::compareTo);
            State nextState = openList.remove(0);
            while (nextState.previous != currentState && !openList.isEmpty()) {
                nextState = openList.remove(0);
            }

            closeList.add(nextState);
            nextState.previous = currentState;
            currentState = nextState;

            if (currentState.equals(endState)) {
                endState.previous = currentState.previous;
                break;
            }
        } while (!openList.isEmpty());

        List<State> solutionList = new ArrayList<>();
        State previousState = currentState;
        while (null != previousState) {
            solutionList.add(previousState);
            previousState = previousState.previous;
        }

        for (int i = solutionList.size() - 1; i >= 0; i--) {
            solutionList.get(i).print();
            System.out.println("-----------------------");
            System.out.println("-----------------------");
        }
    }

    private double cost(final State state) {
        return g(state) + h(state);
    }


    private int g(final State state) {
        int numMoves = 0;
        State previousState = state.previous;
        while (null != previousState) {
            ++numMoves;
            previousState = previousState.previous;
        }
        return numMoves;
    }

    private double h(final State state) {
        double sumDis = 0;
        for (int i = 0; i < state.digits.length; i++) {
            for (int j = 0; j < state.digits.length; j++) {
                if (0 != state.digits[i][j]) {
                    sumDis += state.calDistance(i, j, endState);
                }
            }
        }

        return sumDis;
    }

    public State buildState(String input) {
        if (null == input || input.trim().length() == 0) {
            return null;
        }

        String[] splitDigits = input.split(" ");
        int[][] digits = new int[rowSize][rowSize];
        for (int i = 0; i < splitDigits.length; i++) {
            int r = i / rowSize;
            int c = i % rowSize;
            digits[r][c] = Integer.parseInt(splitDigits[i]);
        }
        State state = new State(digits, null);
        if (state.isValid()) {
            return state;
        }
        return null;
    }


    class State implements Comparable<State> {
        private int[][] digits;
        private State previous;
        private int emptyTileR;
        private int emptyTileC;
        private double cost;


        private State() {
        }

        public State(int[][] digits, State previous) {
            this.digits = digits;
            this.previous = previous;

            for (int i = 0; i < digits.length; i++) {
                for (int j = 0; j < digits.length; j++) {
                    if (0 == digits[i][j]) {
                        emptyTileR = i;
                        emptyTileC = j;
                        break;
                    }
                }
            }
        }

        public State nextState(int row, int column) {
            State state = new State();
            int[][] digits = new int[rowSize][rowSize];
            for (int i = 0; i < digits.length; i++) {
                for (int j = 0; j < digits.length; j++) {
                    digits[i][j] = this.digits[i][j];
                }
            }
            state.digits = digits;
            state.emptyTileR = this.emptyTileR;
            state.emptyTileC = this.emptyTileC;
            state.previous = this;

            state.swap(row, column);
            return state;
        }

        public boolean isValid() {
            int size = digits.length;
            if (rowSize != size || rowSize != digits[0].length) {
                return false;
            }

            Set<Integer> digitSet = new TreeSet<>();
            for (int i = 0; i < digits.length; i++) {
                for (int j = 0; j < digits.length; j++) {
                    digitSet.add(digits[i][j]);
                }
            }

            Integer max = digitSet.stream().max(Integer::compareTo).get();
            Integer min = digitSet.stream().min(Integer::compareTo).get();

            return max == rowSize * rowSize - 1 && min == 0;
        }


        public void print() {
            System.out.println();
            int size = digits.length;
            for (int i = 0; i < size; i++) {
                for (int j = 0; j < size; j++) {
                    if (digits[i][j] == 0) {
                        System.out.print("  ");
                    } else {
                        System.out.print(digits[i][j] + " ");
                    }
                }
                System.out.println();
            }
        }

        public List<State> availableStates() {
            List<State> availableStates = new ArrayList<>();
            if (emptyTileR > 0) {
                System.out.println(digits[emptyTileR - 1][emptyTileC] + " to the south");
                availableStates.add(this.nextState(emptyTileR - 1, emptyTileC));
            }
            if (emptyTileC > 0) {
                System.out.println(digits[emptyTileR][emptyTileC - 1] + " to the east");
                availableStates.add(this.nextState(emptyTileR, emptyTileC - 1));
            }
            if (emptyTileC < rowSize - 1) {
                System.out.println(digits[emptyTileR][emptyTileC + 1] + " to the west");
                availableStates.add(this.nextState(emptyTileR, emptyTileC + 1));
            }
            if (emptyTileR < rowSize - 1) {
                System.out.println(digits[emptyTileR + 1][emptyTileC] + " to the north");
                availableStates.add(this.nextState(emptyTileR + 1, emptyTileC));
            }

            return availableStates;
        }

        public double calDistance(int r, int c, final State targetState) {
            int tr = -1;
            int tc = -1;
            for (int i = 0; i < targetState.digits.length; i++) {
                for (int j = 0; j < targetState.digits.length; j++) {
                    if (targetState.digits[i][j] == digits[r][c]) {
                        tr = i;
                        tc = j;
                        break;
                    }
                }
            }

            return Math.abs(tr - r) + Math.abs(tc - c);
        }

        private void swap(int row, int column) {
            digits[emptyTileR][emptyTileC] = digits[row][column];
            digits[row][column] = 0;
            emptyTileR = row;
            emptyTileC = column;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) {
                return true;
            }
            if (o == null || getClass() != o.getClass()) {
                return false;
            }
            State state = (State) o;
            return Arrays.deepEquals(this.digits, state.digits);
        }

        @Override
        public int hashCode() {
            return Arrays.deepHashCode(digits);
        }

        @Override
        public int compareTo(State o) {
            return Double.compare(this.cost, o.cost);
        }
    }
}

