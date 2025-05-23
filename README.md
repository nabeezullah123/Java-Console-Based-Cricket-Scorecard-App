import java.util.*;

class Batsman {
    String name;
    int runs;
    int balls;
    boolean onStrike;

    public Batsman(String name, boolean onStrike) {
        this.name = name;
        this.runs = 0;
        this.balls = 0;
        this.onStrike = onStrike;
    }

    public void addRuns(int r) {
        runs += r;
        balls++;
    }

    public void addBall() {
        balls++;
    }

    public String toString() {
        return name + (onStrike ? " *" : "") + " - " + runs + "(" + balls + ")";
    }
}

class CricketScorecard {
    private int totalRuns = 0;
    private int totalWickets = 0;
    private int totalOvers = 0;
    private int ballsThisOver = 0;
    private List<Batsman> batsmen = new ArrayList<>();
    private List<String> outPlayers = new ArrayList<>();
    private Scanner sc;

    public CricketScorecard(Scanner sc) {
        this.sc = sc;
        startInnings();
    }

    public void startInnings() {
        batsmen.clear();
        outPlayers.clear();
        totalRuns = 0;
        totalWickets = 0;
        totalOvers = 0;
        ballsThisOver = 0;

        System.out.print("Enter striker name: ");
        String striker = sc.nextLine();
        batsmen.add(new Batsman(striker, true));
        System.out.print("Enter non-striker name: ");
        String nonStriker = sc.nextLine();
        batsmen.add(new Batsman(nonStriker, false));
    }

    private Batsman getStriker() {
        return batsmen.stream().filter(b -> b.onStrike).findFirst().orElse(null);
    }

    private Batsman getNonStriker() {
        return batsmen.stream().filter(b -> !b.onStrike).findFirst().orElse(null);
    }

    public void addRuns(int runs) {
        totalRuns += runs;
        getStriker().addRuns(runs);
        updateBall();

        if (runs % 2 == 1) {
            swapStrike();
        }
    }

    public void addWicket() {
        totalWickets++;
        getStriker().addBall();
        outPlayers.add(getStriker().name);
        batsmen.removeIf(b -> b.onStrike);

        if (totalWickets < 10) {
            addNewBatsman();
        }

        updateBall();
    }

    public void addExtras(int runs, String type) {
        totalRuns += runs;
        System.out.println(type + " extra runs added: " + runs);
        // wide/no-ball does not count as a legal delivery
    }

    private void updateBall() {
        ballsThisOver++;
        if (ballsThisOver == 6) {
            totalOvers++;
            ballsThisOver = 0;
            swapStrike(); // end of over
        }
    }

    public void swapStrike() {
        for (Batsman b : batsmen) {
            b.onStrike = !b.onStrike;
        }
    }

    private void addNewBatsman() {
        System.out.print("Enter new batsman name: ");
        sc.nextLine(); // consume newline
        String name = sc.nextLine();
        batsmen.add(new Batsman(name, true));
    }

    public void showScorecard() {
        System.out.println("\n--- Scorecard ---");
        System.out.println("Total: " + totalRuns + "/" + totalWickets);
        System.out.println("Overs: " + totalOvers + "." + ballsThisOver);
        System.out.println("\nBatting:");
        for (Batsman b : batsmen) {
            System.out.println(b);
        }
        for (String out : outPlayers) {
            System.out.println(out + " - OUT");
        }
        System.out.println("------------------");
    }

    public void resetMatch() {
        sc.nextLine(); // clear input buffer
        startInnings();
        System.out.println("Match reset.");
    }
}

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        CricketScorecard scorecard = new CricketScorecard(scanner);
        int choice;

        do {
            System.out.println("\n1. Add Runs");
            System.out.println("2. Add Wicket");
            System.out.println("3. Add Extras");
            System.out.println("4. Swap Striker");
            System.out.println("5. Show Scorecard");
            System.out.println("6. Reset Match");
            System.out.println("7. Exit");
            System.out.print("Enter choice: ");
            while (!scanner.hasNextInt()) {
                System.out.println("Please enter a number:");
                scanner.next(); // clear invalid input
            }
            choice = scanner.nextInt();

            switch (choice) {
                case 1:
                    System.out.print("Enter runs scored: ");
                    int runs = scanner.nextInt();
                    scorecard.addRuns(runs);
                    break;
                case 2:
                    scorecard.addWicket();
                    break;
                case 3:
                    System.out.print("Enter extra type (wide/no-ball): ");
                    String type = scanner.next();
                    System.out.print("Enter runs: ");
                    int extraRuns = scanner.nextInt();
                    scorecard.addExtras(extraRuns, type);
                    break;
                case 4:
                    scorecard.swapStrike();
                    System.out.println("Striker swapped.");
                    break;
                case 5:
                    scorecard.showScorecard();
                    break;
                case 6:
                    scorecard.resetMatch();
                    break;
                case 7:
                    System.out.println("Exiting...");
                    break;
                default:
                    System.out.println("Invalid choice.");
            }
        } while (choice != 7);

        scanner.close();
    }
}
