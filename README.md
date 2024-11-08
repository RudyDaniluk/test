import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.TextView;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;
import com.squareup.picasso.Picasso;
import retrofit2.Call;
import retrofit2.Callback;
import retrofit2.Response;
import retrofit2.Retrofit;
import retrofit2.converter.gson.GsonConverterFactory;

public class MainActivity extends AppCompatActivity {

    private static final String BASE_URL = "https://api.nasa.gov/";
    private static final String API_KEY = "SRhamzXLkiTKKksVjWg3TU12VVl1Oeq8xnsitk2o";
    private TextView titleTextView;
    private TextView descriptionTextView;
    private ImageView imageView;
    private Button loadButton;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        titleTextView = findViewById(R.id.titleTextView);
        descriptionTextView = findViewById(R.id.descriptionTextView);
        imageView = findViewById(R.id.imageView);
        loadButton = findViewById(R.id.loadButton);

        loadButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                fetchPhotoOfTheDay();
            }
        });
    }

    private void fetchPhotoOfTheDay() {
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(BASE_URL)
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        ApiService apiService = retrofit.create(ApiService.class);
        Call<NasaPhoto> call = apiService.getPhotoOfTheDay(API_KEY, "2024-11-08"); // Możesz dynamicznie podać datę

        call.enqueue(new Callback<NasaPhoto>() {
            @Override
            public void onResponse(Call<NasaPhoto> call, Response<NasaPhoto> response) {
                if (response.isSuccessful() && response.body() != null) {
                    NasaPhoto photo = response.body();
                    titleTextView.setText(photo.getTitle());
                    descriptionTextView.setText(photo.getExplanation());
                    Picasso.get().load(photo.getUrl()).into(imageView);
                } else {
                    Toast.makeText(MainActivity.this, "Błąd w pobieraniu danych", Toast.LENGTH_SHORT).show();
                }
            }

            @Override
            public void onFailure(Call<NasaPhoto> call, Throwable t) {
                Toast.makeText(MainActivity.this, "Błąd: " + t.getMessage(), Toast.LENGTH_SHORT).show();
            }
        });
    }
}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    tools:context=".MainActivity">

    <!-- Tytuł zdjęcia -->
    <TextView
        android:id="@+id/titleTextView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Tytuł zdjęcia"
        android:textSize="20sp"
        android:textStyle="bold"
        android:paddingBottom="8dp" />

    <!-- Obraz zdjęcia dnia -->
    <ImageView
        android:id="@+id/imageView"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:scaleType="centerCrop"
        android:contentDescription="@string/photo_of_the_day"
        android:layout_marginBottom="8dp" />

    <!-- Opis zdjęcia -->
    <TextView
        android:id="@+id/descriptionTextView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Opis zdjęcia"
        android:textSize="16sp"
        android:paddingBottom="8dp" />

    <!-- Przycisk do pobrania zdjęcia -->
    <Button
        android:id="@+id/loadButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Pobierz zdjęcie dnia" />

</LinearLayout>
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
