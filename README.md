public class MyCustomRectangleView extends View {
    private Paint paint;
    private float left, top, right, bottom;
    private int color;

    public MyCustomRectangleView(Context context) {
        super(context);
        init();
    }

    public MyCustomRectangleView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    private void init() {
        paint = new Paint();
        paint.setAntiAlias(true);
        left = 100;
        top = 100;
        right = 300;
        bottom = 200;
        color = Color.RED;
        paint.setColor(color);
        startAnimations();
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawRect(left, top, right, bottom, paint);
    }

    private void startAnimations() {
        // Animacja zmiany szerokości prostokąta
        ObjectAnimator rightAnimator = ObjectAnimator.ofFloat(this, "right", right, right + 200);
        rightAnimator.setDuration(1000);
        rightAnimator.setRepeatCount(ValueAnimator.INFINITE);
        rightAnimator.setRepeatMode(ValueAnimator.REVERSE);

        // Animacja zmiany wysokości prostokąta
        ObjectAnimator bottomAnimator = ObjectAnimator.ofFloat(this, "bottom", bottom, bottom + 200);
        bottomAnimator.setDuration(1000);
        bottomAnimator.setRepeatCount(ValueAnimator.INFINITE);
        bottomAnimator.setRepeatMode(ValueAnimator.REVERSE);

        // Animacja zmiany pozycji poziomej (lewej krawędzi)
        ObjectAnimator leftAnimator = ObjectAnimator.ofFloat(this, "left", left, left + 300);
        leftAnimator.setDuration(1000);
        leftAnimator.setRepeatCount(ValueAnimator.INFINITE);
        leftAnimator.setRepeatMode(ValueAnimator.REVERSE);

        // Animacja zmiany pozycji pionowej (górnej krawędzi)
        ObjectAnimator topAnimator = ObjectAnimator.ofFloat(this, "top", top, top + 300);
        topAnimator.setDuration(1000);
        topAnimator.setRepeatCount(ValueAnimator.INFINITE);
        topAnimator.setRepeatMode(ValueAnimator.REVERSE);

        // Animacja zmiany koloru
        ValueAnimator colorAnimator = ValueAnimator.ofArgb(Color.RED, Color.BLUE);
        colorAnimator.setDuration(1000);
        colorAnimator.setRepeatCount(ValueAnimator.INFINITE);
        colorAnimator.setRepeatMode(ValueAnimator.REVERSE);
        colorAnimator.addUpdateListener(animator -> {
            color = (int) animator.getAnimatedValue();
            paint.setColor(color);
            invalidate();
        });

        // Uruchomienie animacji
        AnimatorSet animatorSet = new AnimatorSet();
        animatorSet.playTogether(rightAnimator, bottomAnimator, leftAnimator, topAnimator, colorAnimator);
        animatorSet.start();
    }

    public void setLeft(float left) {
        this.left = left;
        invalidate();
    }

    public void setTop(float top) {
        this.top = top;
        invalidate();
    }

    public void setRight(float right) {
        this.right = right;
        invalidate();
    }

    public void setBottom(float bottom) {
        this.bottom = bottom;
        invalidate();
    }

    public void setColor(int color) {
        this.color = color;
        paint.setColor(color);
        invalidate();
    }
}
