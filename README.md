/**
 * The Word class represents a word and its frequency count.
 * It provides methods to set and retrieve the word and its frequency.
 *
 * Author: Bernard Ginn
 * Version: 1.0
 */
class Word {
    private String word;         // Stores the word
    private int frequency;       // Stores the frequency count
    /**
     * Constructor for Word class.
     *
     * @param word      The word to be stored.
     * @param frequency The initial frequency count for the word.
     */
    public Word(String word, int frequency) {
        this.word = word;
        this.frequency = frequency;
    }
    /**
     * Retrieves the stored word.
     *
     * @return The word as a String.
     */
    public String getWord() {
        return word;
    }
    /**
     * Retrieves the frequency count of the word.
     *
     * @return The frequency count as an integer.
     */
    public int getFrequency() {
        return frequency;
    }
    /**
     * Increments the frequency count of the word by one.
     * This is used to update the count when the word appears again.
     */
    public void incrementFrequency() {
        frequency++; 
    }
}
import java.util.Comparator;
/**
 * The WordComparator class is used to compare Word objects based on their frequencies.
 * It implements the Comparator interface to enable sorting of Word objects by frequency.
 *
 * Author: Bernard Ginn
 * Version: 1.0
 */
public class WordComparator extends Word implements Comparator<WordComparator> {
    /**
     * Default constructor for WordComparator.
     * Initializes a WordComparator object with an empty word and frequency of 0.
     */
    public WordComparator() {
        super("", 0);
    }
    /**
     * Parameterized constructor for WordComparator.
     * Initializes a WordComparator object with the provided word and frequency.
     *
     * @param word      The word to compare.
     * @param frequency The frequency of the word.
     */
    public WordComparator(String word, int frequency) {
        super(word, frequency);
    }
    /**
     * Compares two WordComparator objects based on their frequencies.
     * This method is required to implement the Comparator interface.
     *
     * @param w1 The first WordComparator object to compare.
     * @param w2 The second WordComparator object to compare.
     * @return 0 if the frequencies are equal, 1 if w1's frequency is less than w2's,
     *         and -1 if w1's frequency is greater than w2's.
     */
    @Override
    public int compare(WordComparator w1, WordComparator w2) {
        if (w1.getFrequency() == w2.getFrequency()) {
            return 0; // Frequencies are equal
        } else if (w1.getFrequency() < w2.getFrequency()) {
            return 1; // w1's frequency is less than w2's
        } else {
            return -1; // w1's frequency is greater than w2's
        }
    }
}

import java.util.List;
import java.io.File;
import java.util.Scanner;
import java.util.ArrayList;
import java.util.Map;
import java.util.HashMap;
import java.util.Collections;
import java.io.PrintWriter;
import java.nio.charset.StandardCharsets;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.util.Calendar;

/**
 * This class, FileWordCount, serves as a utility for processing a text file.
 * It reads the file, counts the frequency of each word, sorts the words by frequency,
 * and provides options to display the results on the console and save them to an output file.
 *
 * Author: Bernard Ginn
 * Version:1.0
 */
public class FileWordCount {
    // Define the constant file name to read from
    public static final String FILE_NAME ="working_hashing.txt";
    public static void main(String[] args) {
        // Read the contents of the file into a list of words
        List<String> wordList = readFile(FILE_NAME);
        // Create a frequency map for the words
        Map<String, Integer> frequencyMap = getFrequencyMap(wordList);
        // Convert the frequency map into a list of WordComparator objects for sorting
        List<WordComparator> wordComparatorList = getWordList(frequencyMap);
        // Sort the WordComparator list based on word frequency in descending order
        wordComparatorList.sort(new WordComparator());
        // Print the sorted list to the console
        printList(wordComparatorList);
        // Write the results to an output file
        writeToFile(wordComparatorList);
    }
    /**
     * Reads the contents of a file and returns a list of words.
     *
     * @param fileName The name of the file to read.
     * @return A list of words from the file.
     */
    public static List<String> readFile(String fileName) {
        // Initialize a list to store words read from the file
        List<String> wordList = new ArrayList<>();
        try {
            // Create a scanner to read the file using whitespace as a delimiter
            Scanner scanner = new Scanner(new File(fileName)).useDelimiter("\\s+");
            // Read words from the file and add them to the list
            while (scanner.hasNext()) {
                String word = scanner.next();
                wordList.add(word);
            }
            // Close the scanner
            scanner.close();
        } catch (java.io.FileNotFoundException fnfe) {
            fnfe.printStackTrace();
        }
        return wordList;
    }
    /**
     * Creates a frequency map for the words in the given list.
     *
     * @param wordList The list of words.
     * @return A map with words as keys and their frequencies as values.
     */
    public static Map<String, Integer> getFrequencyMap(List<String> wordList) {
        // Initialize a map to store word frequencies
        Map<String, Integer> frequencyMap = new HashMap<>();
        // Count the frequency of each word and store it in the map
        for (String word : wordList) {
            int frequency = Collections.frequency(wordList, word);
            frequencyMap.put(word, frequency);
        }
        return frequencyMap;
    }
    /**
     * Converts the frequency map into a list of WordComparator objects.
     *
     * @param frequencyMap The frequency map.
     * @return A list of WordComparator objects.
     */
    public static List<WordComparator> getWordList(Map<String, Integer> frequencyMap) {
        // Initialize a list to store WordComparator objects
        List<WordComparator> wordList = new ArrayList<>();
        // Create WordComparator objects for each entry in the frequency map
        for (Map.Entry<String, Integer> entry : frequencyMap.entrySet()) {
            String word = entry.getKey();
            int frequency = entry.getValue();
            WordComparator wc = new WordComparator(word, frequency);
            wordList.add(wc);
        }
        return wordList;
    }
    /**
     * Prints the WordComparator list to the console.
     *
     * @param wordList The list of WordComparator objects.
     */
    public static void printList(List<WordComparator> wordList) {
        System.out.println("The paragraph has " + wordList.size() + " words. The frequency of the words is as follows:\n");
        for (WordComparator wc : wordList) {
            System.out.println(wc.getWord() + " : " + wc.getFrequency());
        }
    }
    /**
     * Writes the WordComparator list to an output file.
     *
     * @param wordList The list of WordComparator objects.
     */
    public static void writeToFile(List<WordComparator> wordList) {
        PrintWriter writer = null;
        try {
            // Get the current timestamp to create a unique output file name
            Calendar calendar = Calendar.getInstance();
            long timeInMillis = calendar.getTimeInMillis();
            String outputFile = FILE_NAME + "." + timeInMillis;
            // Create a PrintWriter to write to the output file with UTF-8 encoding
            writer = new PrintWriter(outputFile, StandardCharsets.UTF_8);
            // Write the word frequencies to the file
            writer.println("The paragraph has " + wordList.size() + " words. The frequency of the words is as follows:\n");
            for (WordComparator wc : wordList) {
                writer.println(wc.getWord() + " : " + wc.getFrequency());
            }
            // Close the PrintWriter
            writer.close();
        } catch (IOException e) {
            // Handle any IOException that may occur
        }
    }
}

