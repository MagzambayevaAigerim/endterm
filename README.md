# endterm
package com.company;
import jdk.dynalink.Operation;

import javax.swing.text.html.Option;
import java.sql.*;
import java.util.Scanner;

public class BankAccountDemo{
    public static boolean isLogin=false;

    public static void main(String[] args) throws Exception {
	int option=0;
	Scanner scanner=new Scanner(System.in);
	while(option==0){
	    System.out.println("====SELECT AN OPTION====\n");
        System.out.println("1,Create new account");
        System.out.println("2,Sign In\n");
        while (option<1 || option>2){
            System.out.println("Type your choice: ");
            option=scanner.nextInt();
        }
    }
	switch (option) {
        case 1:
            System.out.println("\n\n====CREATE NEW ACCOUNT====\n");
            System.out.println("Enter first name: ");
            String firstName = scanner.next();
            System.out.println("Enter last name: ");
            String lastName = scanner.next().trim();
            Account account = new Account(firstName, lastName);
            account.register();
            break;
        case 2:
            while (isLogin==false){
            System.out.println("\n\n====SIGN IN====\n");
            System.out.println("Enter your card number: ");
            String cardnumber=scanner.next();
            System.out.println("Enter your pincode: ");
            String pincode=scanner.next();
            Operation operation= new Operation(cardnumber,pincode);
            try {
                Connection c= DataBase.connection();
                Statement stmt4=c.createStatement();
                String sql4= "SELECT * FROM Card WHERE card_number='"+ cardnumber+"'AND pincode='"+pincode+"'";
                ResultSet rs4=stmt4.executeQuery(sql4);
            }
            if (rs4.next()){
                isLogin=true;
                System.out.println("\n\n====LOGIN SUСESS===\n");
                System.out.println("Enter your option: ");
                System.out.println("1.Balance: ");
                System.out.println("2.Deposit: ");
                System.out.println("Send to other person : ");
                int option_user=0;
                while (option_user < 1 || option_user > 3) {
                    System.out.println("\n Type your choice: ");
                    option_user= scanner.nextInt();
                }
                int balance=0;
                switch (option_user){
                    case 1:
                        System.out.println("\n\n====SHOW BALANCE====\n");
                        balance=operation.showBalance(cardnumber);
                        System.out.println(balance+" $");
                        break;
                    case 2:
                        System.out.println("\n\n====OPEN DEPOSIT===\n");
                        int amount=0;
                        while(amount<=0){
                            System.out.println("Type amount: ");
                            amount=scanner.nextInt();
                        }
                        operation.deposit(amount,cardnumber);
                        balance=operation.showBalance(cardnumber);
                        System.out.println("\n Current balance is"+ balance+" $");
                        break;
                    case 3:
                        System.out.println("\n\n====SEND MONEY TO OTHER CARD===\n");
                        System.out.println("Enter number of other client: ");
                        String number_other=scanner.next();
                        int amount_other=0;
                        while (amount_other<=0){
                            System.out.println("Enter amount for other client: ");
                            amount_other=scanner.nextInt();
                        }
                        operation.sendMoneyToOther(amount_other,number_other,cardnumber);
                        System.out.println("\n You sent"+amount_other+" $ to"+number_other);
                        break;
                    default:
                        break;
                }
            }else{
                System.out.println("\n Login fail");
            }
            }catch(Exception e){
                System.out.println(e);
        }
            break;
        default:
            break;
    }
    }
}
package com.company;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.Statment;
public class Account {
    private String firstName;
    private String lastName;
    private Integer balance;
    Account(String firstName,String lastName){
        this.firstName=firstName;
        this.lastName=lastName;
    }
    public Boolean register() throws Exception{
        try {
            Connection c = DataBase.connection();
            Statement stmt = c.createStatement();
            String sql = "INSERT INTO Account VALUES (null,'" + this.firstName + "','" + this.lastName + "'";
            stmt.executeUpdate(sql);
            Statement stmt2 = c.createStatement();
            String sql2 = "SELECT LAST INDERT ID";
            ResultSet rs2 = stmt.executeQuery(sql2);
            int last_account_id = 0;
            while (rs2.next()) {
                last_account_id = rs2.generateCardNumber();
            }
            String cardNumber = this.generateCardNumber;
            String code = this.generatePincode();
            Statement stmt3 = c.createStatement();
            String sql3 = "INSERT INTO Card VALUES(" + last_account_id + "" + cardNumber + "" + code + "";
            stmt3.executeUpdate(sql3);
            Statement stmt4 = c.createStatement();
            String sql4 = "INSERT INTO Balance VALUES(" + last_account_id + "" + cardNumber + "" + code + "";
            stmt3.executeUpdate(sql4);
            c.close();
            System.out.println("\n\n Account was successfully created.\n");
            System.out.println("Card number:" + CardNaumber);
            System.out.println("Pincode" + code);
            return true;
        } catch (Exception e){
            System.out.println(e);
            return false;
        }
        public String generateCardNumber() {
            int length=8;
            String passwordSet="1234567890";
            char [] cardNumber=new char[length];
            for(int i=0; i<length; i++){
                int rand =(int)(Math.randome()*passwordSet.length());
                cardNumber[i]=passwordSet.charAt(rand);
            }
        }
        return new String(cardNumber);
        }
        public String generatePincode(){
        int length=4;
        String passwordSet="1234567890";
        char[] cardNumber=new char[(length)];
        for(int i=0; i<length; i++){
            int rand =(int)(Math.randome()*passwordSet.length());
            cardNumber[i]=passwordSet.charAt(rand);
        }
        return new String(cardNumber);
        }
    }
    package com.company;
import java.sql.DriverManager;;
public class DateBase {
    private static final String dbClassName="com.mysql.cj.jdbc.Driver";
    private static final String CONNECTION="j dbc:mysql://localhost:3306/bank_account_demo";
    private static final String USER="root";
    private static final String PASSWORD="1234";
    public static java.sql.Connection connection() throws Exception {
        Class.forName(dbClassName);
        return DriverManager.getConnection(CONNECTION,USER,PASSWORD);
    }

}
package com.company;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
public class Operation {
    private String cardnumber;
    private String pincode;
    private Integer balance;
    Operation(String cardnumber,String pincode){
        this.cardnumber=cardnumber;
        this.pincode=pincode;
    }
    public Integer showBalance(String cardnumber){
        try {
            Connection c=DateBase.connection();
            Statement stmt=c.createStatement();
            Statement stmt5=c.createStatement();
            String sql5="SELECT * FROM Balance WHERE card_number='"+cardnumber+"'";
            ResultSet rs5=stmt5.executeQuery(sql5);
            while (rs5.next()){
                this.balance= rs5.getInt(2);
            }
        }catch (Exception e){
            System.out.println(e);
        }
        return this.balance;
    }
    public void deposit(Integer amount,String cardnumber){
        try{
            Connection c=DateBase.connection();
            Statement stmt6=c.createStatement();
            String sql6="UPDATE Balance SET balance=balance+'"+amount+"' WHERE card_number='"+cardnumber+"'";
            stmt6.executeUpdate(sql6);
        }catch (Exception e){
            System.out.println(e);
        }
    }
    public void sendMoneyToOther(Integer amount_other,String number_other,String cardnumber){
        try {
            Connection c=DateBase.connection();
            Statement stmt8=c.createStatement();
            String sql8="UPDATE Balance SET balance=balance+'"+amount_other+"'WHERE card_number"+number_other+"'";
            stmt8.executeUpdate(sql8);
            Statement stmt9=c.createStatement();
            String sql9="UPDATE Balance SET balance=balance+'"+amount_other+"'WHERE card_number"+cardnumber+"'";
            stmt9.executeUpdate(sql9);
        }catch (Exception e){
            System.out.println(e);
        }
    }
}
