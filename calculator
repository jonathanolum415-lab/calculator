import javax.swing.*;
import javax.swing.border.*;
import java.awt.*;
import java.awt.event.*;

public class Calculator extends JFrame {

```
private JTextField display;
private JLabel expressionLabel;
private String current = "0";
private String previous = null;
private String operator = null;
private boolean freshInput = false;
private boolean justCalculated = false;

private static final Color BG        = new Color(42, 31, 20);
private static final Color BTN_NUM   = new Color(46, 32, 22);
private static final Color BTN_OP    = new Color(74, 48, 16);
private static final Color BTN_EQ    = new Color(196, 132, 26);
private static final Color BTN_CLR   = new Color(58, 18, 18);
private static final Color DISPLAY   = new Color(13, 26, 14);
private static final Color GREEN     = new Color(125, 255, 107);
private static final Color AMBER     = new Color(232, 162, 54);
private static final Color CREAM     = new Color(240, 230, 211);
private static final Color RED_TEXT  = new Color(255, 123, 123);

public Calculator() {
    setTitle("COMPUTOR");
    setDefaultCloseOperation(EXIT_ON_CLOSE);
    setResizable(false);
    getContentPane().setBackground(BG);
    setLayout(new BorderLayout(0, 0));

    JPanel main = new JPanel(new BorderLayout(0, 12));
    main.setBackground(BG);
    main.setBorder(new EmptyBorder(28, 24, 32, 24));

    // --- Display ---
    JPanel displayPanel = new JPanel(new BorderLayout(0, 4));
    displayPanel.setBackground(DISPLAY);
    displayPanel.setBorder(BorderFactory.createCompoundBorder(
        BorderFactory.createLineBorder(new Color(13, 26, 13), 1),
        new EmptyBorder(10, 14, 10, 14)
    ));

    expressionLabel = new JLabel(" ");
    expressionLabel.setFont(new Font("Monospaced", Font.PLAIN, 11));
    expressionLabel.setForeground(new Color(125, 255, 107, 100));
    expressionLabel.setHorizontalAlignment(SwingConstants.RIGHT);

    display = new JTextField("0");
    display.setEditable(false);
    display.setHorizontalAlignment(JTextField.RIGHT);
    display.setFont(new Font("Monospaced", Font.BOLD, 34));
    display.setForeground(GREEN);
    display.setBackground(DISPLAY);
    display.setBorder(null);
    display.setCaretColor(DISPLAY);

    displayPanel.add(expressionLabel, BorderLayout.NORTH);
    displayPanel.add(display, BorderLayout.CENTER);

    JPanel displayWrapper = new JPanel(new BorderLayout());
    displayWrapper.setBackground(new Color(10, 19, 10));
    displayWrapper.setBorder(new EmptyBorder(4, 4, 4, 4));
    displayWrapper.add(displayPanel);

    main.add(displayWrapper, BorderLayout.NORTH);

    // --- Buttons ---
    JPanel grid = new JPanel(new GridLayout(5, 4, 8, 8));
    grid.setBackground(BG);

    String[][] labels = {
        {"AC", "+/−", "%", "÷"},
        {"7",  "8",   "9", "×"},
        {"4",  "5",   "6", "−"},
        {"1",  "2",   "3", "+"},
        {"0",  "0",   ".", "="}
    };

    for (int r = 0; r < labels.length; r++) {
        for (int c = 0; c < labels[r].length; c++) {
            String lbl = labels[r][c];
            if (r == 4 && c == 1) continue; // zero spans 2 cols — skip duplicate

            JButton btn = makeButton(lbl);

            if (r == 4 && c == 0) {
                // "0" spans 2 columns
                JPanel span = new JPanel(new GridLayout(1, 1));
                span.setBackground(BG);
                // We'll handle this by replacing the grid with a custom panel
            }
            grid.add(btn);
        }
    }

    // Rebuild with proper spanning using GridBagLayout
    JPanel buttons = buildButtonPanel();
    main.add(buttons, BorderLayout.CENTER);

    add(main);
    pack();
    setLocationRelativeTo(null);

    // Keyboard support
    KeyboardFocusManager.getCurrentKeyboardFocusManager()
        .addKeyEventDispatcher(e -> {
            if (e.getID() != KeyEvent.KEY_PRESSED) return false;
            char k = e.getKeyChar();
            int code = e.getKeyCode();
            if (k >= '0' && k <= '9') { inputDigit(String.valueOf(k)); return true; }
            if (k == '.') { inputDot(); return true; }
            if (k == '+') { setOp("+"); return true; }
            if (k == '-') { setOp("−"); return true; }
            if (k == '*') { setOp("×"); return true; }
            if (k == '/') { setOp("÷"); return true; }
            if (k == '=' || code == KeyEvent.VK_ENTER) { calculate(); return true; }
            if (code == KeyEvent.VK_ESCAPE) { clearAll(); return true; }
            if (code == KeyEvent.VK_BACK_SPACE) { backspace(); return true; }
            return false;
        });
}

private JPanel buildButtonPanel() {
    JPanel p = new JPanel(new GridBagLayout());
    p.setBackground(BG);
    GridBagConstraints g = new GridBagConstraints();
    g.insets = new Insets(4, 4, 4, 4);
    g.fill = GridBagConstraints.BOTH;
    g.weightx = 1; g.weighty = 1;

    String[][] rows = {
        {"AC", "+/−", "%", "÷"},
        {"7",  "8",   "9", "×"},
        {"4",  "5",   "6", "−"},
        {"1",  "2",   "3", "+"},
    };

    for (int r = 0; r < rows.length; r++) {
        for (int c = 0; c < rows[r].length; c++) {
            g.gridx = c; g.gridy = r; g.gridwidth = 1;
            p.add(makeButton(rows[r][c]), g);
        }
    }

    // Last row: 0 (span 2), ., =
    g.gridy = 4; g.gridwidth = 2; g.gridx = 0;
    p.add(makeButton("0"), g);
    g.gridwidth = 1; g.gridx = 2;
    p.add(makeButton("."), g);
    g.gridx = 3;
    p.add(makeButton("="), g);

    return p;
}

private JButton makeButton(String label) {
    JButton btn = new JButton(label) {
        @Override protected void paintComponent(Graphics g2) {
            Graphics2D g = (Graphics2D) g2.create();
            g.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
            Color base = (Color) getClientProperty("baseColor");
            Color top = base.brighter();
            if (getModel().isPressed()) {
                g.setColor(base.darker());
                g.fillRoundRect(0, 0, getWidth(), getHeight(), 10, 10);
            } else {
                GradientPaint gp = new GradientPaint(0, 0, top, 0, getHeight(), base);
                g.setPaint(gp);
                g.fillRoundRect(0, 0, getWidth(), getHeight() - 3, 10, 10);
                g.setColor(base.darker().darker());
                g.fillRoundRect(0, getHeight() - 5, getWidth(), 5, 10, 10);
            }
            g.dispose();
            super.paintComponent(g2);
        }
    };

    btn.setFont(new Font("Monospaced", Font.BOLD, 16));
    btn.setFocusPainted(false);
    btn.setBorderPainted(false);
    btn.setContentAreaFilled(false);
    btn.setOpaque(false);
    btn.setPreferredSize(new Dimension(64, 54));
    btn.setCursor(Cursor.getPredefinedCursor(Cursor.HAND_CURSOR));

    // Colors per type
    switch (label) {
        case "AC": case "+/−": case "%":
            btn.putClientProperty("baseColor", BTN_CLR);
            btn.setForeground(RED_TEXT);
            if (label.equals("+/−") || label.equals("%"))
                btn.setForeground(CREAM);
            btn.putClientProperty("baseColor", new Color(46, 22, 22));
            break;
        case "÷": case "×": case "−": case "+":
            btn.putClientProperty("baseColor", BTN_OP);
            btn.setForeground(AMBER);
            btn.setFont(new Font("Monospaced", Font.BOLD, 20));
            break;
        case "=":
            btn.putClientProperty("baseColor", BTN_EQ);
            btn.setForeground(new Color(30, 14, 2));
            btn.setFont(new Font("Monospaced", Font.BOLD, 22));
            break;
        default:
            btn.putClientProperty("baseColor", BTN_NUM);
            btn.setForeground(CREAM);
    }

    btn.addActionListener(e -> handleButton(label));
    btn.addMouseListener(new MouseAdapter() {
        Color base = (Color) btn.getClientProperty("baseColor");
        public void mouseEntered(MouseEvent e) {
            btn.putClientProperty("baseColor", base.brighter());
            btn.repaint();
        }
        public void mouseExited(MouseEvent e) {
            btn.putClientProperty("baseColor", base);
            btn.repaint();
        }
    });

    return btn;
}

private void handleButton(String label) {
    switch (label) {
        case "AC":   clearAll(); break;
        case "+/−":  toggleSign(); break;
        case "%":    percent(); break;
        case "÷":    setOp("÷"); break;
        case "×":    setOp("×"); break;
        case "−":    setOp("−"); break;
        case "+":    setOp("+"); break;
        case "=":    calculate(); break;
        case ".":    inputDot(); break;
        default:     inputDigit(label);
    }
}

private void inputDigit(String d) {
    if (freshInput || justCalculated) {
        current = d; freshInput = false; justCalculated = false;
    } else {
        if (current.equals("0")) current = d;
        else if (current.length() < 12) current += d;
    }
    updateDisplay();
}

private void inputDot() {
    if (freshInput || justCalculated) {
        current = "0."; freshInput = false; justCalculated = false;
    } else if (!current.contains(".")) {
        current += ".";
    }
    updateDisplay();
}

private void setOp(String op) {
    if (operator != null && !freshInput) calculate(true);
    previous = current;
    operator = op;
    freshInput = true;
    justCalculated = false;
    expressionLabel.setText(previous + " " + op);
}

private void calculate() { calculate(false); }

private void calculate(boolean chain) {
    if (operator == null || previous == null) return;
    double a = Double.parseDouble(previous);
    double b = Double.parseDouble(current);
    double result;
    expressionLabel.setText(previous + " " + operator + " " + current + " =");
    switch (operator) {
        case "+":  result = a + b; break;
        case "−":  result = a - b; break;
        case "×":  result = a * b; break;
        case "÷":
            if (b == 0) { current = "Error"; updateDisplay(); return; }
            result = a / b; break;
        default:   return;
    }
    // Clean up floating point
    if (result == Math.floor(result) && !Double.isInfinite(result))
        current = String.valueOf((long) result);
    else
        current = String.valueOf(Math.round(result * 1e10) / 1e10);

    if (!chain) { operator = null; previous = null; justCalculated = true; }
    updateDisplay();
}

private void clearAll() {
    current = "0"; previous = null; operator = null;
    freshInput = false; justCalculated = false;
    expressionLabel.setText(" ");
    updateDisplay();
}

private void toggleSign() {
    if (current.equals("0") || current.equals("Error")) return;
    current = current.startsWith("-") ? current.substring(1) : "-" + current;
    updateDisplay();
}

private void percent() {
    if (current.equals("Error")) return;
    double val = Double.parseDouble(current) / 100;
    current = val == Math.floor(val) ? String.valueOf((long) val) : String.valueOf(val);
    updateDisplay();
}

private void backspace() {
    if (current.equals("Error")) { current = "0"; }
    else if (current.length() > 1) current = current.substring(0, current.length() - 1);
    else current = "0";
    updateDisplay();
}

private void updateDisplay() {
    display.setText(current);
    int len = current.length();
    int size = len > 10 ? 20 : len > 7 ? 26 : 34;
    display.setFont(new Font("Monospaced", Font.BOLD, size));
}

public static void main(String[] args) {
    SwingUtilities.invokeLater(() -> {
        try { UIManager.setLookAndFeel(UIManager.getCrossPlatformLookAndFeelClassName()); }
        catch (Exception ignored) {}
        new Calculator().setVisible(true);
    });
}
```

}