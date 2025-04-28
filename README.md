import java.util.*;

abstract class User {
    protected String id, name;
    protected int age;

    public User(String id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}

class Voter extends User {
    private String password;
    private boolean hasVoted;

    public Voter(String id, String name, int age, String password) {
        super(id, name, age);
        this.password = password;
        this.hasVoted = false;
    }

    public boolean checkPassword(String password) {
        return this.password.equals(password);
    }

    public boolean hasVoted() {
        return hasVoted;
    }

    public void setHasVoted(boolean hasVoted) {
        this.hasVoted = hasVoted;
    }
}

class Candidate extends User {
    private String party;
    private int voteCount;

    public Candidate(String id, String name, int age, String party) {
        super(id, name, age);
        this.party = party;
        this.voteCount = 0;
    }

    public String getParty() {
        return party;
    }

    public int getVoteCount() {
        return voteCount;
    }

    public void incrementVoteCount() {
        this.voteCount++;
    }
}

class ElectionManager {
    Map<String, Voter> voters = new HashMap<>();
    Map<String, Candidate> candidates = new HashMap<>();

    void registerVoter(Scanner sc) {
        System.out.print("Enter Voter Name, Age, ID, Password (space separated): ");
        String[] input = sc.nextLine().split(" ");
        voters.put(input[2], new Voter(input[2], input[0], Integer.parseInt(input[1]), input[3]));
        System.out.println("Voter Registered.");
    }

    void registerCandidate(Scanner sc) {
        System.out.print("Enter Candidate Name, Age, ID, Party (space separated): ");
        String[] input = sc.nextLine().split(" ");
        candidates.put(input[2], new Candidate(input[2], input[0], Integer.parseInt(input[1]), input[3]));
        System.out.println("Candidate Registered.");
    }

    void castVote(Scanner sc) {
        System.out.print("Enter your Voter ID: ");
        String voterId = sc.nextLine();
        Voter voter = voters.get(voterId);

        if (voter == null) {
            System.out.println("Invalid Voter ID.");
            return;
        }

        System.out.print("Enter your Password: ");
        String password = sc.nextLine();
        if (!voter.checkPassword(password)) {
            System.out.println("Incorrect Password.");
            return;
        }

        if (voter.hasVoted()) {
            System.out.println("You have already voted.");
            return;
        }

        System.out.println("Available Candidates:");
        candidates.values().forEach(c -> System.out.println(c.getId() + ". " + c.getName() + " (" + c.getParty() + ")"));
        System.out.print("Enter the ID of the candidate you want to vote for: ");
        String candidateId = sc.nextLine();
        Candidate candidate = candidates.get(candidateId);

        if (candidate == null) {
            System.out.println("Invalid Candidate ID.");
            return;
        }

        candidate.incrementVoteCount();
        voter.setHasVoted(true);
        System.out.println("Vote cast successfully for " + candidate.getName() + ".");
    }

    void viewResults() {
        if (candidates.isEmpty()) {
            System.out.println("No candidates registered yet.");
            return;
        }
        System.out.println("\n--- Election Results ---");
        candidates.values().forEach(c -> System.out.println(c.getName() + " (" + c.getParty() + ") - Votes: " + c.getVoteCount()));
        System.out.println("-----------------------\n");
    }
}

public class OnlineVotingSystem {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        ElectionManager manager = new ElectionManager();
        while (true) {
            System.out.println("1. Register Voter\n2. Register Candidate\n3. Cast Vote\n4. View Results\n5. Exit");
            System.out.print("Choose: ");
            int choice = sc.nextInt();
            sc.nextLine(); // Consume newline left-over
            switch (choice) {
                case 1 -> manager.registerVoter(sc);
                case 2 -> manager.registerCandidate(sc);
                case 3 -> manager.castVote(sc);
                case 4 -> manager.viewResults();
                case 5 -> {
                    sc.close();
                    System.out.println("Exiting the Voting System. Goodbye!");
                    return;
                }
                default -> System.out.println("Invalid choice. Please try again.");
            }
        }
    }
}
