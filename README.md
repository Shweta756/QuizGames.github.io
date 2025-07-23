# QuizGames.github.io
QuizGames
package shwetamanjunath;

import java.util.*;

class Question {
    String question;
    String[] options;
    int correctAnswer;
    int amount;

    public Question(String question, String[] options, int correctAnswer, int amount) {
        this.question = question;
        this.options = options;
        this.correctAnswer = correctAnswer;
        this.amount = amount;
    }
}

public class QuizGame {
    private static final String GREEN = "\u001B[32m";
    private static final String RED = "\u001B[31m";
    private static final String RESET = "\u001B[0m";

    private static final List<Question> questions = new ArrayList<>();
    private static final Scanner scanner = new Scanner(System.in);
    private static boolean usedAudiencePoll = false;
    private static boolean used5050 = false;
    private static boolean usedSkip = false;
    private static int totalEarnings = 0;

    private static String playerName;
    private static String playerCity;
    private static int playerAge;

    public static void main(String[] args) {
        // Get player details
        System.out.print("Enter your name: ");
        playerName = scanner.nextLine();
        System.out.print("Enter your city: ");
        playerCity = scanner.nextLine();
        System.out.print("Enter your age: ");
        playerAge = scanner.nextInt();
        scanner.nextLine(); // consume leftover newline

        System.out.println("\nWelcome " + playerName + " from " + playerCity + " (Age: " + playerAge + ")!");
        System.out.println("Let's begin the Quiz!\n");

        initializeQuestions();

        for (int i = 0; i < questions.size(); i++) {
            if (!askQuestion(questions.get(i))) {
                System.out.println("Game over! " + playerName + ", you leave with Rs " + totalEarnings);
                return;
            }
        }

        System.out.println(GREEN + "🎉 Congratulations " + playerName + "! You answered all questions!" + RESET);
        System.out.println("From " + playerCity + " | Age: " + playerAge);
        System.out.println("Your total earnings: Rs " + totalEarnings);
    }

    private static void initializeQuestions() {
        questions.add(new Question("What is the chemical symbol for gold?",
                new String[]{"1. Go", "2. Au", "3. Ag", "4. Fe"}, 2, 50));
        questions.add(new Question("What is the colour of an Apple?",
                new String[]{"1. Green", "2. Red", "3. Yellow", "4. Blue"}, 1, 100));
        questions.add(new Question("What comes after Sunday?",
                new String[]{"1. Badday", "2. Goodday", "3. Monday", "4. Blueday"}, 3, 200));
        questions.add(new Question("What is the capital of France?",
                new String[]{"1. Paris", "2. Berlin", "3. Madrid", "4. Rome"}, 1, 300));
        questions.add(new Question("What is 2+2?",
                new String[]{"1. 3", "2. 4", "3. 5", "4. 6"}, 2, 400));
        questions.add(new Question("Which one of these is an OTT platform?",
                new String[]{"1. Mango", "2. Lenovo", "3. Denmark", "4. Netflix"}, 4, 500));
        questions.add(new Question("Which is the language spoken in Karnataka?",
                new String[]{"1. Kashmiri", "2. Kannada", "3. Kannad", "4. Kannadi"}, 2, 600));
        questions.add(new Question("Who is Prime Minister of India?",
                new String[]{"1. Modi", "2. Trump", "3. Godi", "4. Romeo"}, 1, 700));
        questions.add(new Question("What is 2*17?",
                new String[]{"1. 34", "2. 24", "3. 50", "4. 800"}, 1, 800));
        questions.add(new Question("Which colour is present in India's flag?",
                new String[]{"1. Pink", "2. Orange", "3. Yellow", "4. Red"}, 2, 900));
        questions.add(new Question("What is Dust + Bin?",
                new String[]{"1. binbin", "2. DustDust", "3. binDust", "4. Dustbin"}, 4, 1000));
    }

    private static boolean askQuestion(Question question) {
        System.out.println("\n" + question.question);
        for (String option : question.options) {
            System.out.println(option);
        }

        System.out.print("Choose your answer (1-4) or use lifeline (a = Audience Poll, b = 50:50, c = Skip): ");
        String input = scanner.nextLine();

        if (input.equalsIgnoreCase("a") && !usedAudiencePoll) {
            useAudiencePoll(question);
            usedAudiencePoll = true;
            return askQuestion(question);
        } else if (input.equalsIgnoreCase("b") && !used5050) {
            use5050(question);
            used5050 = true;
            return askQuestion(question);
        } else if (input.equalsIgnoreCase("c") && !usedSkip) {
            System.out.println("⏩ You used the Skip lifeline! Moving to the next question.");
            usedSkip = true;
            return true;
        } else if (input.matches("[1-4]")) {
            int answer = Integer.parseInt(input);
            if (answer == question.correctAnswer) {
                totalEarnings = question.amount;
                System.out.println(GREEN + "Correct! You've earned Rs " + question.amount + RESET);
                return true;
            } else {
                int fallback = getFallbackAmount(question.amount);
                totalEarnings = fallback;
                System.out.println(RED + "Incorrect. The correct answer was option " + question.correctAnswer + RESET);
                System.out.println("You leave with Rs " + fallback);
                return false;
            }
        } else {
            System.out.println("Invalid input. Please choose again.");
            return askQuestion(question);
        }
    }

    private static int getFallbackAmount(int currentAmount) {
        if (currentAmount >= 1000) return 1000;
        else if (currentAmount >= 500) return 500;
        else if (currentAmount >= 200) return 200;
        else return 0;
    }

    private static void useAudiencePoll(Question question) {
        System.out.println("📊 Audience Poll lifeline used.");
        System.out.println("Audience suggests the correct answer is option " + question.correctAnswer);
    }

    private static void use5050(Question question) {
        System.out.println("🔍 50:50 lifeline used. Two incorrect options removed:");
        int removed = 0;
        for (int i = 0; i < question.options.length && removed < 2; i++) {
            if (i + 1 != question.correctAnswer && Math.random() < 0.5) {
                System.out.println("Removed option: " + question.options[i]);
                removed++;
            }
        }
    }
}
