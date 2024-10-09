import java.io.FileWriter;
import java.io.IOException;
import java.security.SecureRandom;

public class CodeGenerator {
    private final String code;

    // Konstruktor przyjmujący długość kodu
    public CodeGenerator(int length) {
        if (length == 4 || length == 6) {
            this.code = generateNumericCode(length);
        } else if (length == 12 || length == 16) {
            this.code = generateAlphanumericCode(length);
        } else {
            throw new IllegalArgumentException("Nieprawidłowa długość kodu. Dozwolone wartości: 4, 6, 12, 16.");
        }
    }

    // Metoda generująca kod składający się z cyfr (0-9)
    private String generateNumericCode(int length) {
        SecureRandom random = new SecureRandom();
        StringBuilder codeBuilder = new StringBuilder(length);
        for (int i = 0; i < length; i++) {
            codeBuilder.append(random.nextInt(10)); // Generowanie liczby z zakresu 0-9
        }
        return codeBuilder.toString();
    }

    // Metoda generująca kod składający się z cyfr, małych i dużych liter (0-9, a-z, A-Z)
    private String generateAlphanumericCode(int length) {
        SecureRandom random = new SecureRandom();
        String characters = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
        StringBuilder codeBuilder = new StringBuilder(length);
        for (int i = 0; i < length; i++) {
            int index = random.nextInt(characters.length());
            codeBuilder.append(characters.charAt(index));
        }
        return codeBuilder.toString();
    }

    // Metoda zwracająca wygenerowany kod
    public String getCode() {
        return this.code;
    }

    // Metoda zapisująca kod do pliku
    public void saveToFile(String filePath) {
        try (FileWriter writer = new FileWriter(filePath)) {
            writer.write(this.code);
            System.out.println("Kod został zapisany do pliku: " + filePath);
        } catch (IOException e) {
            System.err.println("Błąd zapisu do pliku: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        // Przykład użycia
        CodeGenerator pin4 = new CodeGenerator(4);
        System.out.println("Wygenerowany PIN (4-cyfrowy): " + pin4.getCode());
        
        CodeGenerator token12 = new CodeGenerator(12);
        System.out.println("Wygenerowany Token (12-znakowy): " + token12.getCode());

        // Zapis do pliku
        token12.saveToFile("token.txt");
    }
}
