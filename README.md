package fich1;

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.logging.Level;
import java.util.logging.Logger;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JTextField;


public class Fich1 extends JFrame{

   JPanel lienzo;
   Connection connection;
   
   public static void main(String[] args) {
       Fich1 fichaje = new Fich1();
       fichaje.conectar();
       fichaje.mostrarInterfaz();
   }
   
   public Fich1(){
       
   }
   
   public void conectar(){
        String url = "jdbc:postgresql://localhost:5432/postgres";
        String usuarioPostgres = "postgres";
        String contraseniaPostgres = "serrano123";
        try{
            Class.forName("org.postgresql.Driver");
            connection = DriverManager.getConnection(url,usuarioPostgres,contraseniaPostgres);
        }
        catch(ClassNotFoundException | SQLException e){
            System.out.println("conexion no realizada");
        
        }
    }
   
   public void mostrarInterfaz(){
        
        lienzo = new JPanel();
        this.setSize(800,800);
        this.setDefaultCloseOperation(EXIT_ON_CLOSE);
        lienzo.setLayout(null);
        this.getContentPane().add(lienzo);
        this.setVisible(true);
        JTextField carnet = new JTextField();
        JButton sacarFicha = new JButton("Sacar Ficha");
        carnet.setBounds(100,100,100,50);
        sacarFicha.setBounds(100,200,100,50);
        lienzo.updateUI();
        lienzo.add(sacarFicha);
        lienzo.add(carnet);
        
        ActionListener oyenteDeAccion;
        oyenteDeAccion = new ActionListener(){
            
            @Override
            public void actionPerformed(ActionEvent e) {
                if(connection != null){
                    try {
                        Pattern patron = Pattern.compile("[0-9]{6,7}");
                        Matcher mat = patron.matcher(carnet.getText());
                        if(mat.matches()){
                            int carnetValidado = Integer.parseInt(carnet.getText());
                            insertarFichaYCarnet(carnetValidado);
                            JOptionPane.showMessageDialog(null,"Su ficha asignada es: " + devolverFicha());
                        }
                    } catch (SQLException ex) {
                        System.out.println("No se puedo sacar ficha");
                    }
                }

            }
        };
        sacarFicha.addActionListener(oyenteDeAccion);
   }
  
   public void insertarFichaYCarnet(int carnet) throws SQLException{
      try{
          Statement st = connection.createStatement();
          ResultSet rs = st.executeQuery("SELECT insertarcarnet('"+ carnet +"')");
      }
      catch (SQLException ex){
        System.out.println("No se ingresaron datos a la base");
          
     }
   }
      
   public int devolverFicha(){
       int ficha = 0;
       try{
          Statement st = connection.createStatement();
          ResultSet rs = st.executeQuery("SELECT devolverficha()");
          while(rs.next()){
              ficha = rs.getInt(1);
          }
      }
      catch (SQLException ex){
        System.out.println("No se ingresaron datos a la base");
          
     }
       return ficha;
   }
}
