import java.util.ArrayList;
import java.util.Date;
import java.util.List;

// Klasa Person - obiekt, którego stan będziemy zapisywać i przywracać.
class Person {
    private String imie;
    private String nazwisko;
    private Date dataUrodzenia;
    private String email;
    private long telefon;

    public Person(String imie, String nazwisko, Date dataUrodzenia, String email, long telefon) {
        this.imie = imie;
        this.nazwisko = nazwisko;
        this.dataUrodzenia = dataUrodzenia;
        this.email = email;
        this.telefon = telefon;
    }

    // Metoda tworzy obiekt Memento zawierający aktualny stan Person
    public Memento saveToMemento() {
        return new Memento(imie, nazwisko, dataUrodzenia, email, telefon);
    }

    // Metoda przywraca stan na podstawie przekazanego Memento
    public void restoreFromMemento(Memento memento) {
        this.imie = memento.getImie();
        this.nazwisko = memento.getNazwisko();
        this.dataUrodzenia = memento.getDataUrodzenia();
        this.email = memento.getEmail();
        this.telefon = memento.getTelefon();
    }

    @Override
    public String toString() {
        return "Person{" +
                "imie='" + imie + '\'' +
                ", nazwisko='" + nazwisko + '\'' +
                ", dataUrodzenia=" + dataUrodzenia +
                ", email='" + email + '\'' +
                ", telefon=" + telefon +
                '}';
    }

    // Klasa wewnętrzna Memento, która przechowuje stan obiektu Person
    public static class Memento {
        private final String imie;
        private final String nazwisko;
        private final Date dataUrodzenia;
        private final String email;
        private final long telefon;

        public Memento(String imie, String nazwisko, Date dataUrodzenia, String email, long telefon) {
            this.imie = imie;
            this.nazwisko = nazwisko;
            this.dataUrodzenia = dataUrodzenia;
            this.email = email;
            this.telefon = telefon;
        }

        // Gettery pozwalające na odczyt stanu
        public String getImie() {
            return imie;
        }

        public String getNazwisko() {
            return nazwisko;
        }

        public Date getDataUrodzenia() {
            return dataUrodzenia;
        }

        public String getEmail() {
            return email;
        }

        public long getTelefon() {
            return telefon;
        }
    }
}

// Klasa PersonBackup - zarządza listą stanów (pamiątek)
class PersonBackup {
    private final List<Person.Memento> mementoList = new ArrayList<>();

    // Dodanie nowego stanu do listy
    public void addMemento(Person.Memento memento) {
        mementoList.add(memento);
    }

    // Pobranie konkretnego stanu na podstawie indeksu
    public Person.Memento getMemento(int index) {
        if (index < 0 || index >= mementoList.size()) {
            throw new IndexOutOfBoundsException("Invalid index for memento.");
        }
        return mementoList.get(index);
    }
}

// Przykład działania
public class Main {
    public static void main(String[] args) {
        // Tworzenie obiektu Person
        Person person = new Person("Jan", "Kowalski", new Date(), "jan.kowalski@example.com", 123456789);

        // Tworzenie obiektu PersonBackup
        PersonBackup backup = new PersonBackup();

        // Zapisanie początkowego stanu
        backup.addMemento(person.saveToMemento());
        System.out.println("Początkowy stan: " + person);

        // Dokonanie zmian w obiekcie
        person.restoreFromMemento(new Person.Memento("Adam", "Nowak", new Date(), "adam.nowak@example.com", 987654321));
        System.out.println("Zmieniony stan: " + person);

        // Przywrócenie wcześniejszego stanu
        person.restoreFromMemento(backup.getMemento(0));
        System.out.println("Przywrócony stan: " + person);
    }
}
