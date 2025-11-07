import javax.swing.*;
import java.awt.*;

class TreeNodes {
    int data;
    TreeNodes left, right;

    public TreeNodes(int data) {
        this.data = data;
        left = right = null;
    }
}

class BinaryTrees {
    private TreeNodes root;

    public BinaryTrees() {
        root = null;
    }

    public void insert(int data) {
        if (!search(data)) {
            root = insertRec(root, data);
        } else {
            System.out.println("Duplicate value! " + data + " already exists in the tree.");
        }
    }

    private TreeNodes insertRec(TreeNodes root, int data) {
        if (root == null) return new TreeNodes(data);
        if (data < root.data) root.left = insertRec(root.left, data);
        else if (data > root.data) root.right = insertRec(root.right, data);
        return root;
    }

    public void delete(int data) {
        root = deleteRec(root, data);
    }

    private TreeNodes deleteRec(TreeNodes root, int data) {
        if (root == null) return root;
        if (data < root.data) root.left = deleteRec(root.left, data);
        else if (data > root.data) root.right = deleteRec(root.right, data);
        else {
            if (root.left == null) return root.right;
            if (root.right == null) return root.left;
            root.data = minValue(root.right);
            root.right = deleteRec(root.right, root.data);
        }
        return root;
    }

    private int minValue(TreeNodes root) {
        while (root.left != null) root = root.left;
        return root.data;
    }

    public boolean search(int data) {
        return searchRec(root, data);
    }

    private boolean searchRec(TreeNodes root, int data) {
        if (root == null) return false;
        if (root.data == data) return true;
        return data < root.data ? searchRec(root.left, data) : searchRec(root.right, data);
    }

    public String inorder() {
        StringBuilder sb = new StringBuilder();
        inorderRec(root, sb);
        return sb.toString();
    }

    private void inorderRec(TreeNodes root, StringBuilder sb) {
        if (root != null) {
            inorderRec(root.left, sb);
            sb.append(root.data).append(" ");
            inorderRec(root.right, sb);
        }
    }

    public String preorder() {
        StringBuilder sb = new StringBuilder();
        preorderRec(root, sb);
        return sb.toString();
    }

    private void preorderRec(TreeNodes root, StringBuilder sb) {
        if (root != null) {
            sb.append(root.data).append(" ");
            preorderRec(root.left, sb);
            preorderRec(root.right, sb);
        }
    }

    public String postorder() {
        StringBuilder sb = new StringBuilder();
        postorderRec(root, sb);
        return sb.toString();
    }

    private void postorderRec(TreeNodes root, StringBuilder sb) {
        if (root != null) {
            postorderRec(root.left, sb);
            postorderRec(root.right, sb);
            sb.append(root.data).append(" ");
        }
    }
}

public class BinaryTreeVisualizer extends JFrame {
    private BinaryTrees tree;
    private JTextField inputField;
    private JTextArea outputArea;

    public BinaryTreeVisualizer() {
        tree = new BinaryTrees();
        setTitle("Binary Tree Visualizer");
        setSize(600, 400);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        JPanel inputPanel = new JPanel();
        inputField = new JTextField(10);
        JButton insertButton = new JButton("Insert");
        JButton deleteButton = new JButton("Delete");
        JButton searchButton = new JButton("Search");
        JButton inorderButton = new JButton("Inorder");
        JButton preorderButton = new JButton("Preorder");
        JButton postorderButton = new JButton("Postorder");
        JButton clearButton = new JButton("Clear");

        inputPanel.add(new JLabel("Enter Value:"));
        inputPanel.add(inputField);
        inputPanel.add(insertButton);
        inputPanel.add(deleteButton);
        inputPanel.add(searchButton);
        inputPanel.add(inorderButton);
        inputPanel.add(preorderButton);
        inputPanel.add(postorderButton);
        inputPanel.add(clearButton);

        outputArea = new JTextArea();
        outputArea.setEditable(false);
        JScrollPane scrollPane = new JScrollPane(outputArea);
        add(inputPanel, BorderLayout.NORTH);
        add(scrollPane, BorderLayout.CENTER);

        insertButton.addActionListener(e -> processInput(() -> {
            int value = Integer.parseInt(inputField.getText());
            tree.insert(value);
            appendToOutput("Inserted: " + value + "\n");
        }));

        deleteButton.addActionListener(e -> processInput(() -> {
            int value = Integer.parseInt(inputField.getText());
            tree.delete(value);
            appendToOutput("Deleted: " + value + "\n");
        }));

        searchButton.addActionListener(e -> processInput(() -> {
            int value = Integer.parseInt(inputField.getText());
            boolean found = tree.search(value);
            appendToOutput("Search: " + value + " " + (found ? "Found" : "Not Found") + "\n");
        }));

        inorderButton.addActionListener(e -> appendToOutput("Inorder: " + tree.inorder() + "\n"));
        preorderButton.addActionListener(e -> appendToOutput("Preorder: " + tree.preorder() + "\n"));
        postorderButton.addActionListener(e -> appendToOutput("Postorder: " + tree.postorder() + "\n"));
        clearButton.addActionListener(e -> outputArea.setText(""));
    }

    private void processInput(Runnable action) {
        String input = inputField.getText().trim();
        if (input.isEmpty()) {
            appendToOutput("Input is empty! Please enter a number.\n");
            return;
        }
        try {
            action.run();
            inputField.setText("");
        } catch (NumberFormatException ex) {
            appendToOutput("Invalid input! Please enter a number.\n");
        }
    }

    private void appendToOutput(String text) {
        outputArea.append(text);
        outputArea.setCaretPosition(outputArea.getDocument().getLength()); // Scroll to the bottom
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new BinaryTreeVisualizer().setVisible(true));
    }
}
