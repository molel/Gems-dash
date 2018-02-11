package sample;
import javafx.animation.KeyFrame;
import javafx.animation.Timeline;
import javafx.fxml.FXML;
import javafx.scene.canvas.Canvas;
import javafx.scene.canvas.GraphicsContext;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.image.*;
import javafx.scene.input.MouseEvent;
import javafx.scene.text.Font;
import javafx.util.Duration;
public class Controller {
    public static int Score = 145000, nasti = -1, nastj = -1, nasti2, nastj2, anlz = 0, numberOfBombs = 0, numberOfGems = 100;
    public static boolean opportunity = false, opportunity2 = true, ex = false, opportunity3 = true, s = true;
    public static Stone[][] stones = new Stone[6][6];

    @FXML
    private Canvas can;

    @FXML
    private Canvas can2;

    @FXML
    private void starter() {
        if (s) {
            can2.setLayoutY(0);
            can2.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("fon.jpg")), 0, 0);
            can2.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("bomb.jpg")), 75, 125);
            GraphicsContext graphicsContext = can.getGraphicsContext2D();
            for (int i = 0; i < 6; i++) {
                for (int j = 0; j < 6; j++) {
                    stones[i][j] = nazn(StdRandom.uniform(5));
                    draw(i, j, graphicsContext);
                }
            }
            search(graphicsContext);
            s = false;
        }
    }

    @FXML
    public void s(MouseEvent event) {
        try {
            if (Score < 150000) {
                if (stones[0][0] != null) {
                    GraphicsContext graphicsContext = can.getGraphicsContext2D();
                    if (event.getSceneX() >= 49 && event.getSceneX() <= 49 + 98 && event.getSceneY() >= 15 && event.getSceneY() <= 15 + 46) {
                        numberOfGems = 0;
                        numberOfBombs = 0;
                        Score = 0;
                        destroying2(graphicsContext);
                    }
                    if (event.getSceneX() >= 75 && event.getSceneX() <= 125 && event.getSceneY() >= 125 && event.getSceneY() <= 175) {
                        ex = true;
                    }
                    if (event.getSceneX() >= 75 && event.getSceneX() <= 125 && event.getSceneY() >= 185 && event.getSceneY() <= 235 && numberOfGems > 0) {
                        destroying(graphicsContext);
                    }
                    if (event.getSceneX() > 200) {
                        if (nasti == -1 && nastj == -1) {
                            nasti = Math.toIntExact(Math.round(((event.getSceneX() - 200) - (event.getSceneX() - 200) % 50) / 50));
                            nastj = Math.toIntExact(Math.round((event.getSceneY() - event.getSceneY() % 50) / 50));
                            graphicsContext.strokeRect(nasti * 50 + 2, nastj * 50 + 2, 46, 46);
                            if (ex && numberOfBombs > 0) {
                                explosion(graphicsContext, nasti, nastj);
                            }
                        } else {
                            nasti2 = Math.toIntExact(Math.round(((event.getSceneX() - 200) - (event.getSceneX() - 200) % 50) / 50));
                            nastj2 = Math.toIntExact(Math.round((event.getSceneY() - event.getSceneY() % 50) / 50));
                            int F = Math.abs(nasti - nasti2) + Math.abs(nastj - nastj2);
                            if (F == 1 && stones[nasti][nastj].name.equals(stones[nasti2][nastj2]) == false) {
                                exchange(nasti, nastj, nasti2, nastj2, graphicsContext);
                                Stone localStone = stones[nasti][nastj];
                                stones[nasti][nastj] = stones[nasti2][nastj2];
                                stones[nasti2][nastj2] = localStone;
                                draw(nasti, nastj, graphicsContext);
                                draw(nasti2, nastj2, graphicsContext);
                                search(graphicsContext);
                                if (anlz == 0) {
                                    Stone[] localStone1 = new Stone[]{stones[nasti][nastj]};
                                    stones[nasti][nastj] = stones[nasti2][nastj2];
                                    stones[nasti2][nastj2] = localStone1[0];
                                    int[] a = new int[]{nasti, nastj, nasti2, nastj2};
                                    Timeline timeline = new Timeline(
                                            new
                                                    KeyFrame(
                                                    Duration.millis(400),
                                                    ae -> {
                                                        exchange(a[0], a[1], a[2], a[3], graphicsContext);
                                                    }
                                            )
                                    );
                                    timeline.setCycleCount(1);
                                    timeline.play();
                                    Timeline timeline1 = new Timeline(
                                            new
                                                    KeyFrame(
                                                    Duration.millis(530),
                                                    ae1 -> {
                                                        draw(a[0], a[1], graphicsContext);
                                                        draw(a[2], a[3], graphicsContext);
                                                    }
                                            )
                                    );
                                    timeline1.setCycleCount(1);
                                    timeline1.play();
                                    exchange(a[2], a[3], a[0], a[1], graphicsContext);
                                }
                                nasti = -1;
                                nastj = -1;
                            } else {
                                if (F > 1) {
                                    draw(nasti, nastj, graphicsContext);
                                    nasti = nasti2;
                                    nastj = nastj2;
                                    graphicsContext.strokeRect(nasti * 50 + 2, nastj * 50 + 2, 46, 46);
                                }
                            }
                        }
                    }
                }
            } else {
                can.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("win.jpg")), 20, 100);
            }
        } catch (Exception w) {
            System.out.print("!");
        }
    }

    @FXML
    public void destroying2(GraphicsContext graphicsContext) {
        for (int i =0; i <= 5; i++) {
            for (int j = 0; j <= 5; j++) {
                stones[i][j].crowd = true;
                Image[] im = new Image[]{new Image(getClass().getResourceAsStream("clear_step1.jpg")), new Image(getClass().getResourceAsStream("clear_step2.jpg")), new Image(getClass().getResourceAsStream("clear_step3.jpg")), new Image(getClass().getResourceAsStream("null.jpg"))};
                int[] a = new int[]{0, i * 50, j * 50};
                Timeline timeline = new Timeline(
                        new KeyFrame(
                                Duration.millis(100),
                                ae -> {
                                    graphicsContext.drawImage(im[a[0]], a[1], a[2]);
                                    a[0]++;
                                    if (a[0] == 3) {
                                        opportunity = true;
                                    }
                                }
                        )
                );
                timeline.setCycleCount(4);
                timeline.play();
            }
        }
        Timeline timeline = new Timeline(
                new KeyFrame(
                        Duration.millis(450),
                        ae -> {
                            falling(graphicsContext);
                        }
                )
        );
        timeline.setCycleCount(1);
        timeline.play();
    }

    @FXML
    public void destroying(GraphicsContext graphicsContext) {
        numberOfGems--;
        Score += 36 * 15;
        for (int i =0; i <= 5; i++) {
            for (int j = 0; j <= 5; j++) {
                stones[i][j].crowd = true;
                Image[] im = new Image[]{new Image(getClass().getResourceAsStream("clear_step1.jpg")), new Image(getClass().getResourceAsStream("clear_step2.jpg")), new Image(getClass().getResourceAsStream("clear_step3.jpg")), new Image(getClass().getResourceAsStream("null.jpg"))};
                int[] a = new int[]{0, i * 50, j * 50};
                Timeline timeline = new Timeline(
                        new KeyFrame(
                                Duration.millis(100),
                                ae -> {
                                    graphicsContext.drawImage(im[a[0]], a[1], a[2]);
                                    a[0]++;
                                    if (a[0] == 3) {
                                        opportunity = true;
                                    }
                                }
                        )
                );
                timeline.setCycleCount(4);
                timeline.play();
            }
        }
        Timeline timeline = new Timeline(
                new KeyFrame(
                        Duration.millis(450),
                        ae -> {
                            falling(graphicsContext);
                        }
                )
        );
        timeline.setCycleCount(1);
        timeline.play();
        if (Score >= 150000) {
            can.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("win.jpg")), 20, 100);
        }
    }

    @FXML
    public void explosion(GraphicsContext graphicsContext, int x, int y) {
        numberOfBombs--;
        ex = false;
        int snx = 1, kx = 1, sny = 1, ky = 1;
        if (x == 0) {
            snx = 0;
        }
        if (x == 5) {
            kx = 0;
        }
        if (y == 0) {
            sny = 0;
        }
        if (y == 5) {
            ky = 0;
        }
        for (int i = x - snx; i <= x + kx; i++) {
            for (int j = y - sny; j <= y + ky; j++) {
                stones[i][j].crowd = true;
                Image[] im = new Image[]{new Image(getClass().getResourceAsStream("clear_step1.jpg")), new Image(getClass().getResourceAsStream("clear_step2.jpg")), new Image(getClass().getResourceAsStream("clear_step3.jpg")), new Image(getClass().getResourceAsStream("null.jpg"))};
                int[] a = new int[]{0, i * 50, j * 50};
                Timeline timeline = new Timeline(
                        new KeyFrame(
                                Duration.millis(100),
                                ae -> {
                                    graphicsContext.drawImage(im[a[0]], a[1], a[2]);
                                    a[0]++;
                                    if (a[0] == 3) {
                                        opportunity = true;
                                    }
                                }
                        )
                );
                timeline.setCycleCount(4);
                timeline.play();
            }
        }
            Timeline timeline = new Timeline(
                    new KeyFrame(
                            Duration.millis(450),
                            ae -> {
                                falling(graphicsContext);
                            }
                    )
            );
            timeline.setCycleCount(1);
            timeline.play();
        nasti = -1;
        nastj = -1;
        Score += 9 * 15;
        if (Score >= 150000) {
            can.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("win.jpg")), 20, 100);
        }
    }

    @FXML
    public void exchange(int x, int y, int x2, int y2, GraphicsContext graphicsContext) {
        int[] coordinatesX = new int[]{x * 50, x2 * 50};
        int[] coordinatesY = new int[]{y * 50, y2 * 50};
        Image image = findImage(x, y), image1 = findImage(x2, y2);
        if (y == y2) {
            if (x > x2) {
                Timeline timeline = new Timeline(
                        new
                                KeyFrame(
                                Duration.millis(10),
                                ae -> {
                                    coordinatesX[0] -= 5;
                                    coordinatesX[1] += 5;
                                    graphicsContext.drawImage(image, coordinatesX[0], coordinatesY[0]);
                                    graphicsContext.drawImage(image1, coordinatesX[1], coordinatesY[1]);
                                }
                        )
                );
                timeline.setCycleCount(10);
                timeline.play();
            } else {
                if (x < x2) {
                    Timeline timeline = new Timeline(
                            new
                                    KeyFrame(
                                    Duration.millis(10),
                                    ae -> {
                                        coordinatesX[0] += 5;
                                        coordinatesX[1] -= 5;
                                        graphicsContext.drawImage(image, coordinatesX[0], coordinatesY[0]);
                                        graphicsContext.drawImage(image1, coordinatesX[1], coordinatesY[1]);
                                    }
                            )
                    );
                    timeline.setCycleCount(10);
                    timeline.play();
                }
            }
        } else {
            if (x == x2) {
                if (y > y2) {
                    Timeline timeline = new Timeline(
                            new
                                    KeyFrame(
                                    Duration.millis(10),
                                    ae -> {
                                        coordinatesY[0] -= 5;
                                        coordinatesY[1] += 5;
                                        graphicsContext.drawImage(image, coordinatesX[0], coordinatesY[0]);
                                        graphicsContext.drawImage(image1, coordinatesX[1], coordinatesY[1]);
                                    }
                            )
                    );
                    timeline.setCycleCount(10);
                    timeline.play();
                } else {
                    Timeline
                            timeline = new Timeline(
                            new
                                    KeyFrame(
                                    Duration.millis(10),
                                    ae -> {
                                        coordinatesY[0] += 5;
                                        coordinatesY[1] -= 5;
                                        graphicsContext.drawImage(image, coordinatesX[0], coordinatesY[0]);
                                        graphicsContext.drawImage(image1, coordinatesX[1], coordinatesY[1]);
                                    }
                            )
                    );
                    timeline.setCycleCount(10);
                    timeline.play();
                }
            }
        }
    }

    @FXML
    public Image findImage(int i, int j) {
        Image image = null;
        if (stones[i][j].crowd) {
            image = new Image(getClass().getResourceAsStream("null.jpg"));
        } else {
            switch (stones[i][j].name) {
                case "BLUE":
                    image = new Image(getClass().getResourceAsStream("ice.jpg"));
                    break;
                case "RED":
                    image = new Image(getClass().getResourceAsStream("fire.jpg"));
                    break;
                case "GREEN":
                    image = new Image(getClass().getResourceAsStream("death.jpg"));
                    break;
                case "YELLOW":
                    image = new Image(getClass().getResourceAsStream("shield.jpg"));
                    break;
                case "VIOLET":
                    image = new Image(getClass().getResourceAsStream("stone.jpg"));
                    break;
            }
        }
        return image;
    }

    @FXML
    public void falling(GraphicsContext graphicsContext) {
        for (int i = 0; i < 6; i++) {
            int num = 0, jh = 0;
            for (int j = 5; j >= 0; j--) {

                if (stones[i][j].crowd) {
                    num++;
                    if (num == 1) {
                        jh = j;
                    }
                }
                if (stones[i][j].crowd == false && num > 0) {
                    int[] coordinates = new int[]{i * 50 + 1, j * 50 + 6}, a = new int[]{0, (jh - j) * 10 - 1};
                    Image image = findImage(i, j);
                    Timeline timeline = new Timeline(
                            new KeyFrame(
                                    Duration.millis(8),
                                    ae -> {
                                        opportunity2 = false;
                                        coordinates[1] += 5;
                                        a[0]++;
                                        graphicsContext.drawImage(image, coordinates[0], coordinates[1]);
                                        if ((coordinates[1] - 1) % 50 == 0) {
                                            graphicsContext.drawImage(new Image(getClass().getResourceAsStream("null.jpg")), coordinates[0], coordinates[1] - 50);
                                        }
                                        if (a[0] > a[1]) {
                                            opportunity2 = true;
                                        }
                                    }
                            )
                    );
                    timeline.setCycleCount(a[1]);
                    timeline.play();
                    stones[i][jh].name = stones[i][j].name;
                    stones[i][jh].crowd = false;
                    jh--;
                    stones[i][j].crowd = true;
                }
            }
        }
        if (opportunity2 = true) {
            Timeline timeline = new Timeline(
                    new KeyFrame(
                            Duration.millis(400),
                            ae -> {
                                supernazn(graphicsContext);
                            }
                    )
            );
            timeline.setCycleCount(1);
            timeline.play();
        }
    }

    @FXML
    public void supernazn(GraphicsContext graphicsContext) {
        int[] b = new int[]{5};
        Timeline timeline2 = new Timeline(
                new KeyFrame(
                        Duration.millis(5),
                        ae -> {
                            for (int i = 5; i >= 0; i--) {
                                for (int j = 5; j >= 0; j--) {
                                    if (stones[i][j].crowd) {
                                        b[0] = j;
                                    }
                                }
                            }
                        }
                )
        );
        timeline2.setCycleCount(1);
        timeline2.play();
        int[] c = new int[]{5, b[0]};
        Timeline timeline = new Timeline(
                new KeyFrame(
                        Duration.millis(5),
                        ae -> {
                            if (stones[c[0]][c[1]].crowd) {
                                stones[c[0]][c[1]] = nazn(StdRandom.uniform(5));
                                int[] a = new int[]{3, c[0] * 50, c[1] * 50};
                                Image[] im = new Image[]{findImage(c[0], c[1]), new Image(getClass().getResourceAsStream("clear_step1.jpg")), new Image(getClass().getResourceAsStream("clear_step2.jpg")), new Image(getClass().getResourceAsStream("clear_step3.jpg")), new Image(getClass().getResourceAsStream("null.jpg"))};
                                Timeline timeline1 = new Timeline(
                                        new KeyFrame(
                                                Duration.millis(150),
                                                ae1 -> {
                                                    graphicsContext.drawImage(im[a[0]], a[1], a[2]);
                                                    a[0]--;
                                                }
                                        )
                                );
                                timeline1.setCycleCount(4);
                                timeline1.play();
                            }
                            if (c[0] == 0 && c[1] != 0) {
                                c[0] = 5;
                                c[1]--;
                            } else {
                                c[0]--;
                            }
                        }
                )
        );
        timeline.setCycleCount(36);
        timeline.play();
        Timeline timeline0 = new Timeline(
                new KeyFrame(
                        Duration.millis(500),
                        ae -> {
                            search(graphicsContext);
                        }
                )
        );
        timeline0.setCycleCount(1);
        timeline0.play();

    }

    @FXML
    public void search(GraphicsContext graphicsContext) {
        opportunity = false;
        anlz = 0;
        int num = 0;
        String kind = "";
        for (int j = 0; j < 6; j++) {
            num = 0;
            for (int i = 0; i < 6; i++) {
                if (i == 0) {
                    kind = stones[i][j].name;
                }
                if (kind.equals(stones[i][j].name)) {
                    num++;
                } else {
                    if (num > 2) {
                        if (num == 4) {
                            numberOfBombs++;
                        }
                        if (num == 5) {
                            numberOfGems++;
                        }
                        Score += num * 35;
                        anlz++;
                        for (int k = i - num; k < i; k++) {
                            opportunity = false;
                            Image[] im = new Image[]{new Image(getClass().getResourceAsStream("clear_step1.jpg")), new Image(getClass().getResourceAsStream("clear_step2.jpg")), new Image(getClass().getResourceAsStream("clear_step3.jpg")), new Image(getClass().getResourceAsStream("null.jpg"))};
                            int[] a = new int[]{0, k * 50, j * 50};
                            Timeline timeline = new Timeline(
                                    new KeyFrame(
                                            Duration.millis(100),
                                            ae -> {
                                                graphicsContext.drawImage(im[a[0]], a[1], a[2]);
                                                a[0]++;
                                                if (a[0] == 3) {
                                                    opportunity = true;
                                                }
                                            }
                                    )
                            );
                            timeline.setCycleCount(4);
                            timeline.play();


                            stones[k][j].crowd = true;
                        }
                    }
                    kind = stones[i][j].name;
                    num = 1;
                }
                if (i == 5 && num > 2) {
                    if (num == 4) {
                        numberOfBombs++;
                    }
                    if (num == 5) {
                        numberOfGems++;
                    }
                    opportunity = false;
                    Score += num * 35;
                    anlz++;
                    for (int k = 6 - num; k < 6; k++) {
                        stones[k][j].crowd = true;
                        Image[] im = new Image[]{new Image(getClass().getResourceAsStream("clear_step1.jpg")), new Image(getClass().getResourceAsStream("clear_step2.jpg")), new Image(getClass().getResourceAsStream("clear_step3.jpg")), new Image(getClass().getResourceAsStream("null.jpg"))};
                        int[] a = new int[]{0, k * 50, j * 50};
                        Timeline timeline = new Timeline(
                                new KeyFrame(
                                        Duration.millis(100),
                                        ae -> {
                                            graphicsContext.drawImage(im[a[0]], a[1], a[2]);
                                            a[0]++;
                                            if (a[0] == 3) {
                                                opportunity = true;
                                            }
                                        }
                                )
                        );
                        timeline.setCycleCount(4);
                        timeline.play();
                    }
                }
            }
        }
        num = 0;
        kind = "";
        for (int i = 0; i < 6; i++) {
            num = 0;
            for (int j = 0; j < 6; j++) {
                if (j == 0) {
                    kind = stones[i][j].name;
                }
                if (kind.equals(stones[i][j].name)) {
                    num++;
                } else {
                    if (num > 2) {
                        if (num == 4) {
                            numberOfBombs++;
                        }
                        if (num == 5) {
                            numberOfGems++;
                        }
                        opportunity = false;
                        Score += num * 35;
                        anlz++;
                        for (int k = j - num; k < j; k++) {
                            Image[] im = new Image[]{new Image(getClass().getResourceAsStream("clear_step1.jpg")), new Image(getClass().getResourceAsStream("clear_step2.jpg")), new Image(getClass().getResourceAsStream("clear_step3.jpg")), new Image(getClass().getResourceAsStream("null.jpg"))};
                            int[] a = new int[]{0, i * 50, k * 50};
                            Timeline timeline = new Timeline(
                                    new KeyFrame(
                                            Duration.millis(100),
                                            ae -> {
                                                graphicsContext.drawImage(im[a[0]], a[1], a[2]);
                                                a[0]++;
                                                if (a[0] == 3) {
                                                    opportunity = true;
                                                }
                                            }
                                    )
                            );
                            timeline.setCycleCount(4);
                            timeline.play();
                            stones[i][k].crowd = true;
                        }
                    }
                    kind = stones[i][j].name;
                    num = 1;
                }
                if (j == 5 && num > 2) {
                    if (num == 4) {
                        numberOfBombs++;
                    }
                    if (num == 5) {
                        numberOfGems++;
                    }
                    opportunity = false;
                    Score += num * 35;
                    anlz++;
                    for (int k = 6 - num; k < 6; k++) {
                        stones[i][k].crowd = true;
                        Image[] im = new Image[]{new Image(getClass().getResourceAsStream("clear_step1.jpg")), new Image(getClass().getResourceAsStream("clear_step2.jpg")), new Image(getClass().getResourceAsStream("clear_step3.jpg")), new Image(getClass().getResourceAsStream("null.jpg"))};
                        int[] a = new int[]{0, i * 50, k * 50 };
                        Timeline timeline = new Timeline(
                                new KeyFrame(
                                        Duration.millis(100),
                                        ae -> {
                                            graphicsContext.drawImage(im[a[0]], a[1], a[2]);
                                            a[0]++;
                                            if (a[0] == 3) {
                                                opportunity = true;
                                            }
                                        }
                                )
                        );
                        timeline.setCycleCount(4);
                        timeline.play();
                    }
                }
            }
        }
        if (anlz > 0) {
            Timeline timeline = new Timeline(
                    new KeyFrame(
                            Duration.millis(400),
                            ae -> {
                                falling(graphicsContext);
                            }
                    )
            );
            timeline.setCycleCount(1);
            timeline.play();
        }
        can2.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("fon.jpg")), 0, 0);
        can2.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("bomb.jpg")), 75, 125);
        can2.getGraphicsContext2D().fillText(Integer.toString(Score), 80, 90);
        can2.getGraphicsContext2D().fillText("x" + Integer.toString(numberOfBombs), 125, 155);
        can2.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("gem.jpg")), 75, 185);
        can2.getGraphicsContext2D().fillText("x" + Integer.toString(numberOfGems), 125, 215);
        can2.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("restart.jpg")), 49, 15);
        if (Score >= 150000) {
            can.getGraphicsContext2D().drawImage(new Image(getClass().getResourceAsStream("win.jpg")), 20, 100);
        }
    }

    @FXML
    public void draw(int i, int j, GraphicsContext gc) {
        if (stones[i][j].crowd) {
            gc.drawImage(new Image(getClass().getResourceAsStream("null.jpg")), i * 50, j * 50);
        } else {
            switch (stones[i][j].name) {
                case "BLUE":
                    gc.drawImage(new Image(getClass().getResourceAsStream("ice.jpg")), i * 50, j * 50);
                    break;
                case "RED":
                    gc.drawImage(new Image(getClass().getResourceAsStream("fire.jpg")), i * 50, j * 50);
                    break;
                case "GREEN":
                    gc.drawImage(new Image(getClass().getResourceAsStream("death.jpg")), i * 50, j * 50);
                    break;
                case "YELLOW":
                    gc.drawImage(new Image(getClass().getResourceAsStream("shield.jpg")), i * 50, j * 50);
                    break;
                case "VIOLET":
                    gc.drawImage(new Image(getClass().getResourceAsStream("stone.jpg")), i * 50, j * 50);
                    break;
            }
        }
    }

    @FXML
    public Stone nazn(int a) {
        String stoneName = "";
        switch (a) {
            case 0:
                stoneName = "BLUE";
                break;
            case 1:
                stoneName = "RED";
                break;
            case 2:
                stoneName = "GREEN";
                break;
            case 3:
                stoneName = "YELLOW";
                break;
            case 4:
                stoneName = "VIOLET";
                break;
        }
        return new Stone(stoneName);
    }
}
