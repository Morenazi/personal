# personal
estoy trabajando con una IA en una app personal ,no se NADA de esto.

import javafx.application.Application;
import javafx.scene.control.ButtonType;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.DatePicker;
import javafx.scene.control.Label;
import javafx.scene.control.ListView;
import javafx.scene.control.TextArea;
import javafx.scene.control.TextField;
import javafx.scene.image.Image;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

import java.awt.Desktop;
import java.io.FileWriter;
import java.io.IOException;
import java.net.URI;
import java.net.URISyntaxException;

public class OrganizadorApp extends Application {

    private DatePicker fechaPicker;
    private TextField recordatorioField;
    private ListView<String> recordatoriosList;

    @Override
    public void start(Stage primaryStage) {
        // Crear los componentes de la interfaz
        Label fechaLabel = new Label("Fecha:");
        fechaPicker = new DatePicker();
        Label recordatorioLabel = new Label("Recordatorio:");
        recordatorioField = new TextField();
        recordatorioField.setPrefColumnCount(20);
        Button agregarButton = new Button("Agregar recordatorio");
        Label recordatoriosLabel = new Label("Recordatorios:");
        recordatoriosList = new ListView<>();
        Button correosButton = new Button("Correos");
        Button emergenciaButton = new Button("Declarar emergencia");
        Button metasButton = new Button("Metas cumplidas");

        // Asignar las acciones a los botones
        agregarButton.setOnAction(e -> {
            String fecha = fechaPicker.getValue().toString();
            String recordatorio = recordatorioField.getText();
            recordatoriosList.getItems().add("Fecha: " + fecha + " - Recordatorio: " + recordatorio);
            recordatoriosList.getItems().sort((s1, s2) -> s1.substring(6, 16).compareTo(s2.substring(6, 16)));
            recordatorioField.clear();
        });

        correosButton.setOnAction(e -> {
            try {
                Desktop.getDesktop().mail();
            } catch (IOException ex) {
                ex.printStackTrace();
            }
        });

        emergenciaButton.setOnAction(e -> {
            Alert alert = new Alert(Alert.AlertType.ERROR);
            alert.setTitle("Emergencia");
            alert.setHeaderText("Ha ocurrido una emergencia");
            alert.setContentText("¿Desea llamar al número de emergencia?");
            ButtonType callButton = new ButtonType("Llamar");
            alert.getButtonTypes().setAll(callButton, ButtonType.CANCEL);
            alert.showAndWait().ifPresent(response -> {
                if (response == callButton) {
                    try {
                        Desktop.getDesktop().browse(new URI("tel:112"));
                    } catch (IOException | URISyntaxException ex) {
                        ex.printStackTrace();
                    }
                }
            });
        });

        metasButton.setOnAction(e -> {
            Stage metasStage = new Stage();
            metasStage.setTitle("Metas cumplidas");
            metasStage.getIcons().add(new Image("icon.png"));
            Label metasLabel = new Label("¿Qué metas has cumplido hoy?");
            TextArea metasArea = new TextArea();
            metasArea.setPrefRowCount(5);
            Label tiempoLabel = new Label("¿Cuánto tiempo has empleado?");
            TextField tiempoField = new TextField();
            tiempoField.setPrefColumnCount(10);
            Label valoracionLabel = new Label("¿Cómo valoras tu rendimiento?");
            TextField valoracionField = new TextField();
            valoracionField.setPrefColumnCount(10);
            Button guardarButton = new Button("Guardar");
            guardarButton.setOnAction(f -> {
                try {
                    FileWriter writer = new FileWriter("metas.txt", true);
                    writer.append("Metas: " + metasArea.getText() + "\n");
                    writer.append("Tiempo: " + tiempoField.getText() + "\n");
                    writer.append("Valoración: " + valoracionField.getText() + "\n");
                    writer.append("----------\n");
                    writer.close();
                    metasStage.close();
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            });
            VBox metasVbox = new VBox(metasLabel, metasArea, tiempoLabel, tiempoField, valoracionLabel, valoracionField, guardarButton);
            metasVbox.setSpacing(10);
            metasVbox.setPadding(new Insets(10));
            metasVbox.setAlignment(Pos.CENTER);
            Scene metasScene = new Scene(metasVbox, 400, 300);
            metasStage.setScene(metasScene);
            metasStage.show();
        });

        // Crear los paneles de layout
        VBox fechaVbox = new VBox(fechaLabel, fechaPicker);
        VBox recordatorioVbox = new VBox(recordatorioLabel, recordatorioField);
        HBox agregarHbox = new HBox(fechaVbox, recordatorioVbox, agregarButton);
        agregarHbox.setSpacing(10);
        agregarHbox.setPadding(new Insets(10));
        agregarHbox.setAlignment(Pos.CENTER);
        VBox recordatoriosVbox = new VBox(recordatoriosLabel, recordatoriosList);
        recordatoriosVbox.setSpacing(10);
        recordatoriosVbox.setPadding(new Insets(10));
        HBox botonesHbox = new HBox(correosButton, emergenciaButton, metasButton);
        botonesHbox.setSpacing(10);
        botonesHbox.setPadding(new Insets(10));
        botonesHbox.setAlignment(Pos.CENTER);
        BorderPane root = new BorderPane();
        root.setTop(agregarHbox);
        root.setCenter(recordatoriosVbox);
        root.setBottom(botonesHbox);

        // Crear la escena y el escenario
        Scene scene = new Scene(root, 500, 400);
        scene.getStylesheets().add("style.css");
        primaryStage.setTitle("Organizador");
        primaryStage.getIcons().add(new Image("icon.png"));
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    public static void main(String[] args) {
        launch(args);
    }
}

