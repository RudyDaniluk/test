package com.example.customviewapp;

import android.animation.ObjectAnimator;
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.util.AttributeSet;
import android.view.MotionEvent;
import android.view.View;

public class CustomCircleView extends View {
    private Paint paint;
    private float radius;
    private float cx, cy;
    private int color;

    private boolean isDragging = false; // Flaga wskazująca, czy przeciąganie jest aktywne

    public CustomCircleView(Context context) {
        super(context);
        init();
    }

    public CustomCircleView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    private void init() {
        paint = new Paint();
        paint.setAntiAlias(true);
        radius = 100; // Domyślny promień
        cx = 200;     // Domyślna pozycja X
        cy = 200;     // Domyślna pozycja Y
        color = Color.RED; // Domyślny kolor
        paint.setColor(color);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawCircle(cx, cy, radius, paint);
    }

    // Metody do personalizacji widoku
    public void setRadius(float radius) {
        this.radius = radius;
        invalidate(); // Przerysuj widok
    }

    public void setCx(float cx) {
        this.cx = cx;
        invalidate();
    }

    public void setCy(float cy) {
        this.cy = cy;
        invalidate();
    }

    public void setColor(int color) {
        this.color = color;
        paint.setColor(color);
        invalidate();
    }

    // Metoda do uruchamiania animacji zmieniającej rozmiar po upuszczeniu
    private void startSizeAnimation() {
        ObjectAnimator radiusAnimator = ObjectAnimator.ofFloat(this, "radius", radius, radius + 50);
        radiusAnimator.setDuration(1000);
        radiusAnimator.setRepeatCount(ObjectAnimator.INFINITE);
        radiusAnimator.setRepeatMode(ObjectAnimator.REVERSE);
        radiusAnimator.start();
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                // Sprawdź, czy dotyk jest w obrębie okręgu
                float dx = event.getX() - cx;
                float dy = event.getY() - cy;
                if (Math.sqrt(dx * dx + dy * dy) <= radius) {
                    isDragging = true;
                    setColor(Color.BLUE); // Zmień kolor przy rozpoczęciu przeciągania
                    return true;
                }
                break;

            case MotionEvent.ACTION_MOVE:
                if (isDragging) {
                    // Aktualizuj pozycję okręgu w czasie przeciągania
                    cx = event.getX();
                    cy = event.getY();
                    invalidate(); // Przerysuj widok
                    return true;
                }
                break;

            case MotionEvent.ACTION_UP:
                if (isDragging) {
                    isDragging = false; // Zakończ przeciąganie
                    setColor(Color.GREEN); // Zmień kolor po upuszczeniu
                    startSizeAnimation();  // Rozpocznij animację zmiany rozmiaru
                    return true;
                }
                break;
        }
        return super.onTouchEvent(event);
    }
}
