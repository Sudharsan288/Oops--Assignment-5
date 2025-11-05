# Oops--Assignment-5

import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;

/**
 * Single-file demo: JList with a custom cell renderer.
 * Save as CustomListRendererDemo.java
 * Compile: javac CustomListRendererDemo.java
 * Run:     java CustomListRendererDemo
 */
public class CustomListRendererDemo {

    // Simple data class to show in the list
    static class Person {
        final String name;
        final String email;
        final Icon avatar;

        Person(String name, String email, Icon avatar) {
            this.name = name;
            this.email = email;
            this.avatar = avatar;
        }

        @Override
        public String toString() {
            return name; // fallback representation
        }
    }

    // Custom renderer for Person objects
    static class PersonRenderer extends JPanel implements ListCellRenderer<Person> {
        private final JLabel iconLabel = new JLabel();
        private final JLabel nameLabel = new JLabel();
        private final JLabel emailLabel = new JLabel();

        PersonRenderer() {
            setLayout(new BorderLayout(8, 4));
            setBorder(new EmptyBorder(6, 6, 6, 6));

            // left: avatar
            iconLabel.setPreferredSize(new Dimension(48, 48));
            add(iconLabel, BorderLayout.WEST);

            // center: name (bold) and email (smaller)
            JPanel textPanel = new JPanel();
            textPanel.setLayout(new BoxLayout(textPanel, BoxLayout.Y_AXIS));
            nameLabel.setFont(nameLabel.getFont().deriveFont(Font.BOLD, 14f));
            emailLabel.setFont(emailLabel.getFont().deriveFont(Font.PLAIN, 12f));
            emailLabel.setForeground(Color.DARK_GRAY);

            textPanel.add(nameLabel);
            textPanel.add(Box.createVerticalStrut(3));
            textPanel.add(emailLabel);
            textPanel.setOpaque(false);

            add(textPanel, BorderLayout.CENTER);

            // ensure renderer paints background
            setOpaque(true);
        }

        @Override
        public Component getListCellRendererComponent(JList<? extends Person> list,
                                                      Person value,
                                                      int index,
                                                      boolean isSelected,
                                                      boolean cellHasFocus) {
            if (value != null) {
                iconLabel.setIcon(value.avatar);
                nameLabel.setText(value.name);
                emailLabel.setText(value.email);
            } else {
                iconLabel.setIcon(null);
                nameLabel.setText("");
                emailLabel.setText("");
            }

            // selection styling
            if (isSelected) {
                setBackground(list.getSelectionBackground());
                setForeground(list.getSelectionForeground());
            } else {
                setBackground(list.getBackground());
                setForeground(list.getForeground());
            }

            // optionally show focus border
            if (cellHasFocus) {
                setBorder(BorderFactory.createLineBorder(list.getSelectionForeground()));
            } else {
                setBorder(new EmptyBorder(6, 6, 6, 6));
            }

            return this;
        }
    }

    private static Icon createInitialsIcon(String initials, int size) {
        // create a simple circular icon with initials drawn on it
        BufferedImage image = new BufferedImage(size, size, BufferedImage.TYPE_INT_ARGB);
        Graphics2D g = image.createGraphics();
        g.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);

        // background circle
        g.setColor(new Color(120, 170, 255));
        g.fillOval(0, 0, size, size);

        // initials
        g.setColor(Color.white);
        Font font = new Font("SansSerif", Font.BOLD, size / 2);
        g.setFont(font);
        FontMetrics fm = g.getFontMetrics();
        int w = fm.stringWidth(initials);
        int h =fm.getAscent();
        g.drawString(initials, (size - w) / 2, (size + h / 2) / 2 + 2);

        g.dispose();
        return new ImageIcon(image);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            try {
                UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());
            } catch (Exception ignored) {}

            JFrame frame = new JFrame("JList - Custom Cell Renderer Demo");
            frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

            DefaultListModel<Person> model = new DefaultListModel<>();
            model.addElement(new Person("Alice Johnson", "alice@example.com", createInitialsIcon("AJ", 48)));
            model.addElement(new Person("Bob Smith", "bob.smith@example.com", createInitialsIcon("BS", 48)));
            model.addElement(new Person("Carol Lee", "carol.lee@example.com", createInitialsIcon("CL", 48)));
            model.addElement(new Person("David Wu", "david.wu@example.com", createInitialsIcon("DW", 48)));

            JList<Person> list = new JList<>(model);
            list.setCellRenderer(new PersonRenderer());
            list.setSelectionMode(ListSelectionModel.SINGLE_SELECTION);
            list.setFixedCellHeight(70); // helps with consistent spacing

            frame.add(new JScrollPane(list), BorderLayout.CENTER);
            frame.setSize(400, 300);
            frame.setLocationRelativeTo(null);
            frame.setVisible(true);
        });
    }
}
