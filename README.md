# Best-Route-Find-using-graph
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.*;
import java.util.List;

class Graph {
    private Map<String, Map<String, Integer>> adjList = new HashMap<>();

    public void addEdge(String src, String dest, int weight) {
        adjList.computeIfAbsent(src, k -> new HashMap<>()).put(dest, weight);
        adjList.computeIfAbsent(dest, k -> new HashMap<>()).put(src, weight);
    }

    public void printAvailableDestinations() {
        System.out.println("Available cities and their connections:");
        for (Map.Entry<String, Map<String, Integer>> city : adjList.entrySet()) {
            System.out.print(city.getKey() + ": ");
            for (Map.Entry<String, Integer> dest : city.getValue().entrySet()) {
                System.out.print(dest.getKey() + " (" + dest.getValue() + " km), ");
            }
            System.out.println();
        }
    }

    public java.util.List<String> getShortestPath(String src, String dest) {
        Map<String, Integer> distances = new HashMap<>();
        Map<String, String> previous = new HashMap<>();
        PriorityQueue<String> pq = new PriorityQueue<>(Comparator.comparingInt(distances::get));

        for (String city : adjList.keySet()) {
            distances.put(city, Integer.MAX_VALUE);
        }
        distances.put(src, 0);
        pq.add(src);

        while (!pq.isEmpty()) {
            String current = pq.poll();
            for (Map.Entry<String, Integer> neighbor : adjList.get(current).entrySet()) {
                int alt = distances.get(current) + neighbor.getValue();
                if (alt < distances.get(neighbor.getKey())) {
                    distances.put(neighbor.getKey(), alt);
                    previous.put(neighbor.getKey(), current);
                    pq.add(neighbor.getKey());
                }
            }
        }

        java.util.List<String> path = new ArrayList<>();
        for (String at = dest; at != null; at = previous.get(at)) {
            path.add(at);
        }
        Collections.reverse(path);
        return path.get(0).equals(src) ? path : new ArrayList<>();
    }

    public int getPathDistance(String src, String dest) {
        java.util.List<String> path = getShortestPath(src, dest);
        if (path.isEmpty()) return -1;
        int totalDistance = 0;
        for (int i = 0; i < path.size() - 1; i++) {
            totalDistance += adjList.get(path.get(i)).get(path.get(i + 1));
        }
        return totalDistance;
    }

    public Set<String> getCities() {
        return adjList.keySet();
    }

    public Map<String, Map<String, Integer>> getAdjList() {
        return adjList;
    }
}

public class GraphShortestPathFinder {
    private static Graph graph = new Graph();
    private static JComboBox<String> sourceComboBox, destComboBox;
    private static JTextArea resultArea;
    private static JTextField newSourceField, newDestField, weightField;
    private static JPanel visualizationPanel;

    public static void main(String[] args) {
        // Pre-defined edges
        graph.addEdge("Delhi", "Mumbai", 1400);
        graph.addEdge("Delhi", "Kolkata", 1500);
        graph.addEdge("Mumbai", "Chennai", 1330);
        graph.addEdge("Chennai", "Bangalore", 350);
        graph.addEdge("Kolkata", "Chennai", 1650);
        graph.addEdge("Kolkata", "Hyderabad", 1500);
        graph.addEdge("Hyderabad", "Bangalore", 570);
        graph.addEdge("Mumbai", "Bangalore", 980);

        SwingUtilities.invokeLater(GraphShortestPathFinder::createAndShowGUI);
    }

    private static void createAndShowGUI() {
        JFrame frame = new JFrame("Graph Shortest Path Finder");
        frame.setSize(600, 600);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLocationRelativeTo(null);

        JPanel mainPanel = new JPanel(new BorderLayout());
        mainPanel.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));
        frame.add(mainPanel);

        JPanel topPanel = new JPanel(new GridBagLayout());
        GridBagConstraints gbc = new GridBagConstraints();
        gbc.gridx = 0;
        gbc.gridy = 0;
        gbc.anchor = GridBagConstraints.WEST;
        gbc.insets = new Insets(5, 5, 5, 5);

        JLabel sourceLabel = new JLabel("Source:");
        topPanel.add(sourceLabel, gbc);

        sourceComboBox = new JComboBox<>(graph.getCities().toArray(new String[0]));
        gbc.gridx = 1;
        gbc.weightx = 1.0;
        gbc.fill = GridBagConstraints.HORIZONTAL;
        topPanel.add(sourceComboBox, gbc);

        JLabel destLabel = new JLabel("Destination:");
        gbc.gridx = 0;
        gbc.gridy = 1;
        gbc.weightx = 0.0;
        gbc.fill = GridBagConstraints.NONE;
        topPanel.add(destLabel, gbc);

        destComboBox = new JComboBox<>(graph.getCities().toArray(new String[0]));
        gbc.gridx = 1;
        gbc.weightx = 1.0;
        gbc.fill = GridBagConstraints.HORIZONTAL;
        topPanel.add(destComboBox, gbc);

        JButton findPathButton = new JButton("Find Shortest Path");
        gbc.gridx = 0;
        gbc.gridy = 2;
        gbc.gridwidth = 2;
        gbc.weightx = 0.0;
        gbc.fill = GridBagConstraints.NONE;
        topPanel.add(findPathButton, gbc);

        JLabel newSourceLabel = new JLabel("New Source:");
        gbc.gridx = 0;
        gbc.gridy = 3;
        gbc.gridwidth = 1;
        gbc.weightx = 0.0;
        gbc.fill = GridBagConstraints.NONE;
        topPanel.add(newSourceLabel, gbc);

        newSourceField = new JTextField(15);
        gbc.gridx = 1;
        gbc.weightx = 1.0;
        gbc.fill = GridBagConstraints.HORIZONTAL;
        topPanel.add(newSourceField, gbc);

        JLabel newDestLabel = new JLabel("New Destination:");
        gbc.gridx = 0;
        gbc.gridy = 4;
        gbc.weightx = 0.0;
        gbc.fill = GridBagConstraints.NONE;
        topPanel.add(newDestLabel, gbc);

        newDestField = new JTextField(15);
        gbc.gridx = 1;
        gbc.weightx = 1.0;
        gbc.fill = GridBagConstraints.HORIZONTAL;
        topPanel.add(newDestField, gbc);

        JLabel weightLabel = new JLabel("Weight (km):");
        gbc.gridx = 0;
        gbc.gridy = 5;
        gbc.weightx = 0.0;
        gbc.fill = GridBagConstraints.NONE;
        topPanel.add(weightLabel, gbc);

        weightField = new JTextField(15);
        gbc.gridx = 1;
        gbc.weightx = 1.0;
        gbc.fill = GridBagConstraints.HORIZONTAL;
        topPanel.add(weightField, gbc);

        JButton addEdgeButton = new JButton("Add Edge");
        gbc.gridx = 0;
        gbc.gridy = 6;
        gbc.gridwidth = 2;
        gbc.weightx = 0.0;
        gbc.fill = GridBagConstraints.NONE;
        topPanel.add(addEdgeButton, gbc);

        mainPanel.add(topPanel, BorderLayout.NORTH);

        resultArea = new JTextArea();
        resultArea.setEditable(false);
        resultArea.setLineWrap(true);
        resultArea.setWrapStyleWord(true);
        JScrollPane scrollPane = new JScrollPane(resultArea);
        mainPanel.add(scrollPane, BorderLayout.CENTER);

        // Panel for graph visualization
        visualizationPanel = new JPanel() {
            @Override
            protected void paintComponent(Graphics g) {
                super.paintComponent(g);
                drawGraph(g);
            }
        };
        visualizationPanel.setPreferredSize(new Dimension(200, 250));
        visualizationPanel.setBackground(Color.WHITE);
        mainPanel.add(visualizationPanel, BorderLayout.SOUTH);

        findPathButton.addActionListener(e -> findPathButtonClicked());
        addEdgeButton.addActionListener(e -> addEdgeButtonClicked());

        frame.setVisible(true);
    }

    private static void findPathButtonClicked() {
        String src = (String) sourceComboBox.getSelectedItem();
        String dest = (String) destComboBox.getSelectedItem();
        List<String> path = graph.getShortestPath(src, dest);
        int distance = graph.getPathDistance(src, dest);
        if (path.isEmpty()) {
            resultArea.setText("No path found from " + src + " to " + dest);
        } else {
            StringBuilder result = new StringBuilder("Shortest path from " + src + " to " + dest + ":\n");
            for (int i = 0; i < path.size(); i++) {
                result.append(path.get(i));
                if (i < path.size() - 1) {
                    result.append(" -> ");
                }
            }
            result.append("\nTotal distance: ").append(distance).append(" km");
            resultArea.setText(result.toString());
        }
    }

    private static void addEdgeButtonClicked() {
        String newSrc = newSourceField.getText();
        String newDest = newDestField.getText();
        String weightStr = weightField.getText();
        try {
            int weight = Integer.parseInt(weightStr);
            if (!newSrc.isEmpty() && !newDest.isEmpty() && weight > 0) {
                graph.addEdge(newSrc, newDest, weight);
                JOptionPane.showMessageDialog(null, "Edge added successfully!", "Success", JOptionPane.INFORMATION_MESSAGE);
                updateComboBoxes();
                visualizationPanel.repaint();
            } else {
                JOptionPane.showMessageDialog(null, "Invalid input!", "Error", JOptionPane.ERROR_MESSAGE);
            }
        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(null, "Invalid weight!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private static void updateComboBoxes() {
        sourceComboBox.setModel(new DefaultComboBoxModel<>(graph.getCities().toArray(new String[0])));
        destComboBox.setModel(new DefaultComboBoxModel<>(graph.getCities().toArray(new String[0])));
    }

    private static void drawGraph(Graphics g) {
        Graphics2D g2d = (Graphics2D) g;
        g2d.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON); // Smoother lines
        g2d.setColor(Color.BLACK);
        g2d.setStroke(new BasicStroke(2));  // Thicker lines

        Map<String, Point> cityCoordinates = new HashMap<>();
        int radius = Math.min(visualizationPanel.getWidth(), visualizationPanel.getHeight()) / 2 - 50;
        int centerX = visualizationPanel.getWidth() / 2;
        int centerY = visualizationPanel.getHeight() / 2;
        int i = 0;
        int cityCount = graph.getCities().size();
        double angleIncrement = 2 * Math.PI / cityCount;

        // Position cities in a circular layout
        for (String city : graph.getCities()) {
            int x = (int) (centerX + radius * Math.cos(i * angleIncrement));
            int y = (int) (centerY + radius * Math.sin(i * angleIncrement));
            cityCoordinates.put(city, new Point(x, y));
            i++;
        }

        // Draw edges
        for (String src : graph.getCities()) {
            Point srcPoint = cityCoordinates.get(src);
            for (Map.Entry<String, Integer> entry : graph.getAdjList().get(src).entrySet()) {
                String dest = entry.getKey();
                Point destPoint = cityCoordinates.get(dest);
                if (srcPoint != null && destPoint != null) {
                    g2d.drawLine(srcPoint.x, srcPoint.y, destPoint.x, destPoint.y);
                    // Draw edge weight
                    int midX = (srcPoint.x + destPoint.x) / 2;
                    int midY = (srcPoint.y + destPoint.y) / 2;
                    g2d.drawString(entry.getValue().toString(), midX, midY);
                }
            }
        }

        // Draw cities
        for (Map.Entry<String, Point> entry : cityCoordinates.entrySet()) {
            String city = entry.getKey();
            Point point = entry.getValue();
            g2d.setColor(Color.RED);
            g2d.fillOval(point.x - 5, point.y - 5, 10, 10);
            g2d.setColor(Color.BLACK);
            g2d.drawString(city, point.x - 15, point.y - 10);
        }
    }
}

