public class MyCustomView extends View {
    private Paint paint;
    private float radius;
    private float cx, cy;
    private int color;

    public MyCustomView(Context context) {
        super(context);
        init();
    }

    public MyCustomView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    private void init() {
        paint = new Paint();
        paint.setAntiAlias(true);
        radius = 50;
        cx = 100;
        cy = 100;
        color = Color.RED;
        paint.setColor(color);
        startAnimations();
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        canvas.drawCircle(cx, cy, radius, paint);
    }

    private void startAnimations() {
        // Animacja zmiany rozmiaru
        ObjectAnimator radiusAnimator = ObjectAnimator.ofFloat(this, "radius", 50, 150);
        radiusAnimator.setDuration(1000);
        radiusAnimator.setRepeatCount(ValueAnimator.INFINITE);
        radiusAnimator.setRepeatMode(ValueAnimator.REVERSE);

        // Animacja zmiany pozycji
        ObjectAnimator cxAnimator = ObjectAnimator.ofFloat(this, "cx", 100, 500);
        cxAnimator.setDuration(1000);
        cxAnimator.setRepeatCount(ValueAnimator.INFINITE);
        cxAnimator.setRepeatMode(ValueAnimator.REVERSE);

        ObjectAnimator cyAnimator = ObjectAnimator.ofFloat(this, "cy", 100, 500);
        cyAnimator.setDuration(1000);
        cyAnimator.setRepeatCount(ValueAnimator.INFINITE);
        cyAnimator.setRepeatMode(ValueAnimator.REVERSE);

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
        animatorSet.playTogether(radiusAnimator, cxAnimator, cyAnimator, colorAnimator);
        animatorSet.start();
    }

    public void setRadius(float radius) {
        this.radius = radius;
        invalidate();
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
}
