# BHF-StatBlocks
Listing 42 BHF-StatBlocks/StatBlocks.java Page 74  

Cont from. 8.6 Initializing blocks [40.41.BHG-StatOrd](https://github.com/Java-PJATK/40.41.BHG-StatOrd)

In another, a little more complicated example  

## Listing 42 BHF-StatBlocks/StatBlocks.java  

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.URI;
import java.net.URL;
import java.util.Locale;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import java.util.stream.Collectors;
import javax.swing.JOptionPane;

import static java.nio.charset.StandardCharsets.UTF_8;

public class StatBlocks {
    static private double rateUSD = 1;
    static private double rateUAH = 1;

      // static initializer block
    static {
        try {
            URL nbp = new URI(
                "http://www.nbp.pl/kursy/xml/LastA.xml").toURL();
            BufferedReader bw =
                new BufferedReader(
                    new InputStreamReader(
                        nbp.openStream(),UTF_8));
            String txt =
                bw.lines().collect(Collectors.joining(" "));
            bw.close();
            Matcher m =
                Pattern.compile(".*USD.*?(\\d+,\\d+)" +
                                ".*UAH.*?(\\d+,\\d+).*")
                       .matcher(txt);
            m.matches();
            rateUSD = Double.parseDouble(
                         m.group(1).replace(",","."));
            rateUAH = Double.parseDouble(
                         m.group(2).replace(",","."));
        } catch (Exception e) {
            if (JOptionPane.showConfirmDialog(
                    null, "Fetching data failed; continue" +
                    " anyway with default rates = 1?",
                    "WARNING! FETCHING DATA FAILED!",
                    JOptionPane.YES_NO_OPTION
                    ) != JOptionPane.YES_OPTION
               ) System.exit(1);
        }
    }

    private static int ID = 1;

    private final int id;

      // non-static initializer block
    {
        if (ID % 100 == 13) ++ID;
        id = ID++;
    }

    private double amount;

    public StatBlocks(double a) { amount = a; }
    public StatBlocks()         { this(20);   }

    public static double getRateUSD() { return rateUSD; }
    public static double getRateUAH() { return rateUAH; }

    @Override
    public String toString() {
        return String.format(Locale.US,
                "My id = %d. I have %5.2f " +
                "PLN = $%5.2f = %6.2f UAH",id,
                amount, amount/rateUSD,amount/rateUAH);
    }

    public static void main (String[] args)
                          throws Exception {
        double rUSD = StatBlocks.getRateUSD();
        System.out.println("Current rates are:\n" +
            "    USD/PLN = " + rateUSD + '\n' +
            "    UAH/PLN = " + rateUAH);
        StatBlocks sa = new StatBlocks();
        StatBlocks sb = new StatBlocks(40);
        StatBlocks sc = new StatBlocks(80);
        System.out.println(sc + "\n" + sb + "\n" + sa);
    }
}
```

initializing static fields `rateUSD` and `rateUAH` requires getting connection with the site of the National Bank of Poland and fetching the current rates.  

It is done in static initializer block. If fetching rates from the internet fails for some reason, we display a warning asking the user if he/she wants to continue with all rates set to default values 1.

As in the previous example, each created object gets unique identifier id; this time we do it in non-static initializing block. 

Here, to avoid identifiers ending with 13, what might attract a disaster, we skip such numbers, what requires some code to be executed. 

As we do it in the initializer block, we don’t have to repeat the same code in all constructors, what could be easily forgotten about when adding other constructors.  

The program prints something like (the result depends on current rates):  

```java
Current rates are:
    USD/PLN = 4.5066
    UAH/PLN = 0.1258
My id = 3. I have 80.00 PLN = $17.75 = 635.93 UAH
My id = 2. I have 40.00 PLN = $ 8.88 = 317.97 UAH
My id = 1. I have 20.00 PLN = $ 4.44 = 158.98 UAH
```

Next 8.7 Singleton classes [43.44.45.BGX-Singlet](https://github.com/Java-PJATK/43.44.45.BGX-Singlet)  
