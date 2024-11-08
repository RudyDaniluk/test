private int calculateDaysAgo(String date) {
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd", Locale.getDefault());
    try {
        // Parsujemy datę publikacji
        Date publicationDate = sdf.parse(date);
        if (publicationDate != null) {
            // Zamieniamy daty na obiekty Calendar
            Calendar calendarPublication = Calendar.getInstance();
            calendarPublication.setTime(publicationDate);

            Calendar calendarToday = Calendar.getInstance();

            // Obliczamy różnicę w dniach
            long diffInMillis = calendarToday.getTimeInMillis() - calendarPublication.getTimeInMillis();
            long diffInDays = diffInMillis / (1000 * 60 * 60 * 24);  // Przemiana milisekund na dni

            return (int) diffInDays;
        }
    } catch (ParseException e) {
        e.printStackTrace();
    }
    return 0;
}
