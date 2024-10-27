🧱 Brick-Breaking Game 🎮
A classic brick-breaking game built with Java, featuring paddle and ball mechanics, real-time collision detection, and a scoring system. Players use a paddle to keep the ball in play and break bricks for points. This project demonstrates skills in game mechanics, GUI development with Swing, and interactive logic-building.
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;

public class BrickBreaker extends JPanel implements ActionListener {

    private static final int WIDTH = 400;
    private static final int HEIGHT = 300;
    private static final int BRICK_ROWS = 5;
    private static final int BRICK_COLS = 7;
    private static final int BRICK_WIDTH = 50;
    private static final int BRICK_HEIGHT = 20;

    private Timer timer;
    private int ballX = 200, ballY = 200, ballDX = 2, ballDY = -2;
    private int paddleX = 150;
    private boolean[][] bricks = new boolean[BRICK_ROWS][BRICK_COLS];

    public BrickBreaker() {
        setPreferredSize(new Dimension(WIDTH, HEIGHT));
        setBackground(Color.BLACK);
        setFocusable(true);
        addKeyListener(new KeyAdapter() {
            @Override
            public void keyPressed(KeyEvent e) {
                if (e.getKeyCode() == KeyEvent.VK_LEFT && paddleX > 0) {
                    paddleX -= 15;
                }
                if (e.getKeyCode() == KeyEvent.VK_RIGHT && paddleX < WIDTH - 60) {
                    paddleX += 15;
                }
            }
        });

        // Initialize bricks
        for (int i = 0; i < BRICK_ROWS; i++) {
            for (int j = 0; j < BRICK_COLS; j++) {
                bricks[i][j] = true;
            }
        }

        timer = new Timer(10, this);
        timer.start();
    }

    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);

        // Draw bricks
        for (int i = 0; i < BRICK_ROWS; i++) {
            for (int j = 0; j < BRICK_COLS; j++) {
                if (bricks[i][j]) {
                    g.setColor(Color.RED);
                    g.fillRect(j * BRICK_WIDTH, i * BRICK_HEIGHT, BRICK_WIDTH, BRICK_HEIGHT);
                }
            }
        }

        // Draw paddle
        g.setColor(Color.GREEN);
        g.fillRect(paddleX, HEIGHT - 30, 60, 10);

        // Draw ball
        g.setColor(Color.WHITE);
        g.fillOval(ballX, ballY, 15, 15);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        ballX += ballDX;
        ballY += ballDY;

        // Ball collision with walls
        if (ballX < 0 || ballX > WIDTH - 15) {
            ballDX = -ballDX;
        }
        if (ballY < 0) {
            ballDY = -ballDY;
        } else if (ballY > HEIGHT - 15) {
            // Reset ball position if it falls
            ballX = 200;
            ballY = 200;
            ballDX = 2;
            ballDY = -2;
        }

        // Ball collision with paddle
        if (ballY >= HEIGHT - 40 && ballY <= HEIGHT - 30 && ballX >= paddleX && ballX <= paddleX + 60) {
            ballDY = -ballDY;
        }

        // Ball collision with bricks
        for (int i = 0; i < BRICK_ROWS; i++) {
            for (int j = 0; j < BRICK_COLS; j++) {
                if (bricks[i][j]) {
                    int brickX = j * BRICK_WIDTH;
                    int brickY = i * BRICK_HEIGHT;
                    if (ballX + 15 > brickX && ballX < brickX + BRICK_WIDTH && ballY + 15 > brickY && ballY < brickY + BRICK_HEIGHT) {
                        bricks[i][j] = false; // Remove brick
                        ballDY = -ballDY; // Bounce off the brick
                    }
                }
            }
        }

        repaint();
    }

    public static void main(String[] args) {
        JFrame frame = new JFrame("Brick Breaker");
        BrickBreaker game = new BrickBreaker();
        frame.add(game);
        frame.pack();
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLocationRelativeTo(null);
        frame.setVisible(true);
    }
}
