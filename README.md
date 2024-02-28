import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.view.SurfaceHolder;

public class CubeRenderer extends Thread {

    private SurfaceHolder surfaceHolder;
    private boolean isRunning;
    private Paint paint;
    private float angle;

    public CubeRenderer(SurfaceHolder surfaceHolder) {
        this.surfaceHolder = surfaceHolder;
        isRunning = true;
        paint = new Paint();
        angle = 0;
    }

    @Override
    public void run() {
        while (isRunning) {
            Canvas canvas = null;
            try {
                canvas = surfaceHolder.lockCanvas(null);
                synchronized (surfaceHolder) {
                    if (canvas != null) {
                        drawCube(canvas);
                    }
                }
            } finally {
                if (canvas != null) {
                    surfaceHolder.unlockCanvasAndPost(canvas);
                }
            }
            angle += 1; // Zwiększ kąt rotacji
            try {
                Thread.sleep(10); // Poczekaj trochę, aby animacja nie była zbyt szybka
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    private void drawCube(Canvas canvas) {
        canvas.drawColor(Color.WHITE);
        int centerX = canvas.getWidth() / 2;
        int centerY = canvas.getHeight() / 2;
        int size = 200;

        // Rysuj sześcian
        paint.setColor(Color.BLUE);
        drawLine(canvas, centerX - size, centerY - size, centerX + size, centerY - size);
        drawLine(canvas, centerX + size, centerY - size, centerX + size, centerY + size);
        drawLine(canvas, centerX + size, centerY + size, centerX - size, centerY + size);
        drawLine(canvas, centerX - size, centerY + size, centerX - size, centerY - size);

        paint.setColor(Color.RED);
        drawLine(canvas, centerX - size, centerY - size, centerX, centerY - 2 * size);
        drawLine(canvas, centerX + size, centerY - size, centerX, centerY - 2 * size);
        drawLine(canvas, centerX + size, centerY + size, centerX, centerY - 2 * size);
        drawLine(canvas, centerX - size, centerY + size, centerX, centerY - 2 * size);

        paint.setColor(Color.GREEN);
        drawLine(canvas, centerX - size, centerY - size, centerX - 2 * size, centerY);
        drawLine(canvas, centerX - size, centerY + size, centerX - 2 * size, centerY);
        drawLine(canvas, centerX + size, centerY + size, centerX + 2 * size, centerY);
        drawLine(canvas, centerX + size, centerY - size, centerX + 2 * size, centerY);
    }

    private void drawLine(Canvas canvas, float startX, float startY, float endX, float endY) {
        canvas.drawLine(startX, startY, endX, endY, paint);
    }

    public void stopRendering() {
        isRunning = false;
    }
}







.
.
.
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.SurfaceHolder;
import android.view.SurfaceView;

public class MainActivity extends AppCompatActivity {

    private SurfaceView surfaceView;
    private CubeRenderer renderer;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        surfaceView = findViewById(R.id.surfaceView);
        SurfaceHolder holder = surfaceView.getHolder();
        renderer = new CubeRenderer(holder);
    }

    @Override
    protected void onResume() {
        super.onResume();
        renderer.start();
    }

    @Override
    protected void onPause() {
        super.onPause();
        renderer.stopRendering();
    }
}







XML
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <SurfaceView
        android:id="@+id/surfaceView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</RelativeLayout>
