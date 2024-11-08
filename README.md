import android.app.DatePickerDialog;
import java.util.Calendar;

loadButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Calendar calendar = Calendar.getInstance();
        DatePickerDialog datePickerDialog = new DatePickerDialog(MainActivity.this,
            (view, year, month, dayOfMonth) -> {
                String selectedDate = year + "-" + (month + 1) + "-" + dayOfMonth;
                fetchPhotoOfTheDay(selectedDate);
            }, calendar.get(Calendar.YEAR), calendar.get(Calendar.MONTH), calendar.get(Calendar.DAY_OF_MONTH));
        datePickerDialog.show();
    }
});
