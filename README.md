//This game is in Java and includes many classes. I'll paste each one here.

/* Carol Wang
   This is the main class that helps coordinate the different classes and
draw them.
 */

import java.awt.*;
import java.awt.event.*;
import javax.swing.*;
import javax.swing.event.*;

public class MasterClass extends JFrame {
	//to see if level screen was shown
	public static boolean notpicked1=true;
	public static boolean notpicked2=true;
    //to see if level three screen was already shown
    public static boolean shown;
    // to see if question has been correctly answered
    public static boolean timeout;
    // global variables for classes
    public static QuestionScreen qs ;
    public static GameScreen gs;
    public InitialScreen is ;
    // card layout
    public static CardLayout cards;
    // declare images and have an array
    public Image[] images = new Image[6];
    // declare nested classes
    public Level1 lv1;
    Level2 lv2;
    Level3 lv3;
    Over over = new Over();
    Over2 over2=new Over2(); 
    Cor cor = new Cor();
    Incor incor = new Incor();
    WrongScreen wr;
    Store st;
    Instructions in=new Instructions();
    static CardLayoutHolder ch;

    public static void main(String[] hi) {
        MasterClass mc = new MasterClass();
    }

    MasterClass() {
        super("Hungry Panda - Note Edition.");// show this as frame title
        //instantiate classes
        qs = new QuestionScreen();
        gs = new GameScreen();
        is = new InitialScreen();
        lv1 = new Level1();
        lv2 = new Level2();
        lv3 = new Level3();
        wr=new WrongScreen();
        st=new Store();
        ch= new CardLayoutHolder();
        // load images for levels, game over and correct and try again and game over
        images[0] = Toolkit.getDefaultToolkit().getImage("hungrypanda/level1.jpg");
        images[1] = Toolkit.getDefaultToolkit().getImage("hungrypanda/level2.jpg");
        images[2] = Toolkit.getDefaultToolkit().getImage("hungrypanda/level3.jpg");
        images[3] = Toolkit.getDefaultToolkit().getImage("hungrypanda/gameover.jpg");
        images[4] = Toolkit.getDefaultToolkit().getImage("hungrypanda/correct.jpg");
        images[5] = Toolkit.getDefaultToolkit().getImage("hungrypanda/incorrect.jpg");
        setSize(600, 500);// set the size of frame
        setLocation(0, 0);// where top left corner of frame is
        setResizable(true);// cannot be resized
        setDefaultCloseOperation(DISPOSE_ON_CLOSE);// stop running the program after it's closed
        setContentPane(ch);//add cardholder to screen
        cards.show(ch,"Initial");// show initial screen
        setVisible(true);// make frame
    }

	public static void draw1() {//in charge of transitions
        if(InitialScreen.started){
            if (QuestionScreen.answered) {// if answered is true
                if (!QuestionScreen.correct) {// if question is wrong
                	QuestionScreen.wronganswer=true;
                    cards.show(ch, "Incorrect");// show try again image
                    QuestionScreen.wrong.add(QuestionScreen.index);
                    if(QuestionScreen.wrong.size()==5){
                    	QuestionScreen.answered = false;
                    	cards.show(ch, "Game over 2");// show game over image
                        InitialScreen.started=false;
                    }
                }// close if
                else {// else question is right
                    cards.show(ch, "Correct");// show correct image
                    QuestionScreen.answered = false;// back to false so it will draw regular screen
                }// close else
                QuestionScreen.group.clearSelection();
            }// close if

            if (!QuestionScreen.answered) {// else
                if (GameScreen.gameover) {// if game over
                	if(QuestionScreen.wronganswer)
                		cards.show(ch, "Game over 2");// show game over image
                	else
                		cards.show(ch,"Game over");
                    InitialScreen.started=false;
                }// close if

                else if (GameScreen.levels[0]&&notpicked1) {// if level one
                	notpicked1=false;
                    cards.show(ch, "Level 1");// show level 1 image
                }// close if

                else if (GameScreen.levels[1]&&notpicked2) {// else if advanced to level 2
                	notpicked2=false;
                    cards.show(ch, "Level 2");// show level 2 image
                }// close if

                else if (GameScreen.levels[2]&&!shown) {// else if advanced to level 3
                    shown=true;
                    cards.show(ch, "Level 3");// show level 3 image
                } // close if
            }// close else
        }
    }

    public static void draw2(){//to draw questions
        qs.draw();// call draw in question class
        cards.show(ch, "Questions");// show question screen
    }


    public void call(){
        Thread thread = new Thread(new Runnable(){//idea for how to efficiently create a new thread is from http://users.ece.utexas.edu/~ryerraballi/JavaThreads-howto.html by Dr. Ramesh Yerraballi citation #14
            public void run(){
                cards.show(ch, "Game");
                gs.requestFocus();
                gs.drawAndUpdate();//call method to draw and update objects
            }
        });
        thread.start();

    }

    class Level1 extends JPanel implements ActionListener{
        JButton b;
        Level1() {
            b=new JButton("Click to continue");
            b.addActionListener(this);
            add(b);
        }

        public void paintComponent(Graphics g) {
            super.paintComponent(g);
            g.drawImage(images[0], 0, 0, 600, 500, this);// draw image
        }

        public void actionPerformed(ActionEvent e){
            for(int i=gs.bf.badfoodarray.size()-1;i>=0;i--){
                gs.bf.badfoodarray.remove(i);
            }
            for(int i=gs.gf.goodfoodarray.size()-1;i>=0;i--){
                gs.gf.goodfoodarray.remove(i);
            }
            for(int i=gs.cm.coinarray.size()-1;i>=0;i--){
            	gs.cm.coinarray.remove(i);
            }
            for(int i=gs.sm.shieldarray.size()-1;i>=0;i--){
            	gs.sm.shieldarray.remove(i);
            }
            call();
        }
    }

    class Level2 extends JPanel implements ActionListener{
        JButton b;
        Level2() {
            b=new JButton("Click to continue");
            b.addActionListener(this);
            add(b);
        }

        public void paintComponent(Graphics g) {
            super.paintComponent(g);
            g.drawImage(images[1], 0, 0, 600, 500, this);// draw image
        }

        public void actionPerformed(ActionEvent e){
            //set everything back to default
            gs.upgradedalready=false;
            gs.score=0;
            gs.x=0;
            for(int i=gs.bf.badfoodarray.size()-1;i>=0;i--){
                gs.bf.badfoodarray.remove(i);
            }
            for(int i=gs.gf.goodfoodarray.size()-1;i>=0;i--){
                gs.gf.goodfoodarray.remove(i);
            }
            for(int i=gs.cm.coinarray.size()-1;i>=0;i--){
            	gs.cm.coinarray.remove(i);
            }
            for(int i=gs.sm.shieldarray.size()-1;i>=0;i--){
            	gs.sm.shieldarray.remove(i);
            }
            call();
        }

    }

    class Level3 extends JPanel implements ActionListener{
        JButton b;
        Level3() {
            b=new JButton("Click to continue");
            b.addActionListener(this);
            add(b);
        }

        public void paintComponent(Graphics g){
            super.paintComponent(g);
            g.drawImage(images[2], 0, 0, 600, 500, this);// draw image
        }
        public void actionPerformed(ActionEvent e){
            //set everything back to default
            gs.upgradedalready=false;
            gs.gameover=false;
            gs.score=0;
            gs.x=0;
            for(int i=gs.bf.badfoodarray.size()-1;i>=0;i--){
                gs.bf.badfoodarray.remove(i);
            }
            for(int i=gs.gf.goodfoodarray.size()-1;i>=0;i--){
                gs.gf.goodfoodarray.remove(i);
            }
            for(int i=gs.cm.coinarray.size()-1;i>=0;i--){
            	gs.cm.coinarray.remove(i);
            }
            for(int i=gs.sm.shieldarray.size()-1;i>=0;i--){
            	gs.sm.shieldarray.remove(i);
            }
            call();
        }

    }

    class Over extends JPanel implements ActionListener{
        JButton b;
        Over(){
        	b=new JButton("Click to go back to initial screen.");
        	b.addActionListener(this);
        	add(b);
        }
        
        public void paintComponent(Graphics g) {
            super.paintComponent(g);
            g.drawImage(images[3], 0, 0, 600, 500, this);// draw image
            g.setColor(Color.RED);
            g.setFont(new Font("SanSerif",Font.BOLD,30));
            g.drawString("Level: "+(InitialScreen.level+1), 30,40);
            g.drawString("Score: "+gs.score, 420,40);
        }
        public void actionPerformed(ActionEvent e){
        	for(int i=0;i<3;i++){
        		GameScreen.levels[i]=false;
        	}
            for(int i=gs.bf.badfoodarray.size()-1;i>=0;i--){
                gs.bf.badfoodarray.remove(i);
            }
            for(int i=gs.gf.goodfoodarray.size()-1;i>=0;i--){
                gs.gf.goodfoodarray.remove(i);
            }
            for(int i=gs.cm.coinarray.size()-1;i>=0;i--){
            	gs.cm.coinarray.remove(i);
            }
            for(int i=gs.sm.shieldarray.size()-1;i>=0;i--){
            	gs.sm.shieldarray.remove(i);
            }
            GameScreen.gameover = false;
            gs.score=0;
            gs.x=0;
            shown=false;
            notpicked1=true;
            notpicked2=true;
            cards.show(ch,"Initial");
        }
    }

    class Over2 extends JPanel implements ActionListener{
        JButton b;
        Over2(){
        	b=new JButton("Click to go view wrong answers.");
        	b.addActionListener(this);
        	add(b);
        }
        
        public void paintComponent(Graphics g) {
            super.paintComponent(g);
            g.drawImage(images[3], 0, 0, 600, 500, this);// draw image
            g.setColor(Color.RED);
            g.setFont(new Font("SanSerif",Font.BOLD,30));
            g.drawString("Level: "+(InitialScreen.level+1), 30,40);
            g.drawString("Score: "+gs.score, 420,40);
        }
        public void actionPerformed(ActionEvent e){
        	for(int i=0;i<3;i++){
        		GameScreen.levels[i]=false;
        	}
            for(int i=gs.bf.badfoodarray.size()-1;i>=0;i--){
                gs.bf.badfoodarray.remove(i);
            }
            for(int i=gs.gf.goodfoodarray.size()-1;i>=0;i--){
                gs.gf.goodfoodarray.remove(i);
            }
            for(int i=gs.cm.coinarray.size()-1;i>=0;i--){
            	gs.cm.coinarray.remove(i);
            }
            for(int i=gs.sm.shieldarray.size()-1;i>=0;i--){
            	gs.sm.shieldarray.remove(i);
            }
            GameScreen.gameover = false;
            gs.score=0;
            gs.x=0;
            shown=false;
            notpicked1=true;
            notpicked2=true;
            cards.show(ch, "Wrong");
            call2();
        }

    }
    
    public void call2(){
        Thread thread = new Thread(new Runnable(){//idea for how to efficiently create a new thread is from http://users.ece.utexas.edu/~ryerraballi/JavaThreads-howto.html by Dr. Ramesh Yerraballi citation #14
            public void run(){
                wr.repaint();
            }
        });
        thread.start();
    }
    
    class Cor extends JPanel implements ActionListener {
        JButton b;
        Cor() {
            setBackground(Color.WHITE);
            b=new JButton("Click to continue");
            b.addActionListener(this);
            add(b);
        }

        public void paintComponent(Graphics g) {
            super.paintComponent(g);
            g.drawImage(images[4], 50, 0, 500, 500, this);// draw image
        }
        public void actionPerformed(ActionEvent e){
            call();
        }

    }

    class Incor extends JPanel  implements ActionListener{
        JButton b;
        Incor() {
            setBackground(Color.YELLOW);
            b=new JButton("Click to continue");
            b.addActionListener(this);
            add(b);
        }

        public void paintComponent(Graphics g) {
            super.paintComponent(g);
            g.drawImage(images[5], 50, 0, 500, 490, 0, 50, 600, 700,this);// draw image
        }
        public void actionPerformed(ActionEvent e){
            draw2();
        }

    }

    class Instructions extends JPanel implements ActionListener{
        JButton b;
        JLabel l;
        Instructions(){
            setBackground(Color.GREEN);
            setLayout(new FlowLayout(FlowLayout.CENTER,100,50));
            b=new JButton("Click to go back to initial screen");
            b.addActionListener(this);
            add(b);
            l=new JLabel("<html>This game is very simple. You will have a 15 second interval to play <br>a catching food game. All you have to do is press the left and <br>right arrow keys to move the panda. Eat the food and dodge <br>the trash. Your main objective is to get 100 points. Be sure to<br> be ready for the question every 15 seconds. If you get 5 questions<br>wrong, you will lose the game. Of course you will have the opportunity <br>to review them. That's how you will learn. Most importantly, have fun!</html>",SwingConstants.CENTER);//show instruction
            //this html text breaking idea for a JLabel and it works because JLabel has some basic HTML features and it is from this forum: http://stackoverflow.com/questions/1090098/newline-in-jlabel citation #16
            l.setFont(new Font("SanSerif",Font.BOLD,16));
            l.setForeground(new Color(200,0,255));//set purple color
            add(l);
        }
        public void actionPerformed(ActionEvent e){
            if(e.getActionCommand().equals("Click to go back to initial screen")){
                cards.show(ch,"Initial");
            }
        }

        public void paintComponent(Graphics g){
            super.paintComponent(g);
        }
    }

    
    class CardLayoutHolder extends JPanel {// parent for cardlayout
        CardLayoutHolder() {
            cards = new CardLayout();//card layout instantiated
            setLayout(cards);// add card layout
            add(lv1, "Level 1");// level one jpanel image
            add(lv2, "Level 2");// level 2 jpanel image
            add(lv3, "Level 3");// level 3 jpanel image
            add(over, "Game over");// game over jpanel image
            add(over2,"Game over 2");
            add(cor, "Correct");// correct image
            add(incor, "Incorrect");// incorrect image
            add(is, "Initial");// add initial screen
            add(gs, "Game");// add main game screen
            add(qs, "Questions");// add question screen
            add(in, "Instructions");//add instruction panel
            add(wr, "Wrong"); 
            add(st,"Store");
        }
    }
}


/* Carol Wang
This is the initial screen at the start of the game.
 */

import java.awt.*;
import java.awt.event.*;
import javax.swing.*;
import javax.swing.event.*;

public class InitialScreen extends JPanel implements ActionListener{
	//see if game was started by press of button
	public static boolean started;
	//three buttons for each of the levels
	public JButton b1;
	public JButton b2;
	public JButton b3;
	public JButton b4;
	public JButton b5;
	public JLabel title;//jlabel for title
	public static int level;//variable for which level it is on
	public Image background;//image
	InitialScreen(){
		background=Toolkit.getDefaultToolkit().getImage("hungrypanda/background.jpg");//load background image
		setLayout(new FlowLayout(FlowLayout.CENTER,500,30));//set layout
		//instantiate label
		title=new JLabel();
		title.setFont(new Font("SanSerif",Font.BOLD,40));
		title.setText("Hungry Panda - Note Edition");
		//add label
		add(title);
		//instantiate buttons
		b1=new JButton("Easy");
		b2=new JButton("Medium");
		b3=new JButton("Hard");
		b4=new JButton("Instructions");
		b5=new JButton("Store");
		//add action listener
		b1.addActionListener(this);
		b2.addActionListener(this);
		b3.addActionListener(this);
		b4.addActionListener(this);
		b5.addActionListener(this);
		//add buttons to the screen
		add(b4);
		add(b1);
		add(b2);
		add(b3);
		add(b5); 
	
	}
	public void actionPerformed(ActionEvent e){
		GameScreen.gameover=false;//gameover is false
		started=true;
		//see which button was pressed and determine level
		if(e.getActionCommand().equals("Easy")){
			level=0;
			GameScreen.levels[level]=true;
			MasterClass.draw1();
		}
		else if(e.getActionCommand().equals("Medium")){
			level=1;
			GameScreen.levels[level]=true;
			MasterClass.draw1();
		}
		else if(e.getActionCommand().equals("Hard")){
			level=2;
			GameScreen.levels[level]=true;
			MasterClass.draw1();
		}
		else if(e.getActionCommand().equals("Instructions")){
			MasterClass.cards.show(MasterClass.ch,"Instructions");
		}
		else if(e.getActionCommand().equals("Store")){
			MasterClass.cards.show(MasterClass.ch, "Store");
		}
	}
	public void paintComponent(Graphics g){
		super.paintComponent(g);
		g.drawImage(background,0,0,600,500,this);//draw image
	}
}



/* Carol Wang
This is the initial screen at the start of the game.
 */

import java.awt.*;
import java.awt.event.*;
import javax.swing.*;
import javax.swing.event.*;

public class InitialScreen extends JPanel implements ActionListener{
	//see if game was started by press of button
	public static boolean started;
	//three buttons for each of the levels
	public JButton b1;
	public JButton b2;
	public JButton b3;
	public JButton b4;
	public JButton b5;
	public JLabel title;//jlabel for title
	public static int level;//variable for which level it is on
	public Image background;//image
	InitialScreen(){
		background=Toolkit.getDefaultToolkit().getImage("hungrypanda/background.jpg");//load background image
		setLayout(new FlowLayout(FlowLayout.CENTER,500,30));//set layout
		//instantiate label
		title=new JLabel();
		title.setFont(new Font("SanSerif",Font.BOLD,40));
		title.setText("Hungry Panda - Note Edition");
		//add label
		add(title);
		//instantiate buttons
		b1=new JButton("Easy");
		b2=new JButton("Medium");
		b3=new JButton("Hard");
		b4=new JButton("Instructions");
		b5=new JButton("Store");
		//add action listener
		b1.addActionListener(this);
		b2.addActionListener(this);
		b3.addActionListener(this);
		b4.addActionListener(this);
		b5.addActionListener(this);
		//add buttons to the screen
		add(b4);
		add(b1);
		add(b2);
		add(b3);
		add(b5); 
	
	}
	public void actionPerformed(ActionEvent e){
		GameScreen.gameover=false;//gameover is false
		started=true;
		//see which button was pressed and determine level
		if(e.getActionCommand().equals("Easy")){
			level=0;
			GameScreen.levels[level]=true;
			MasterClass.draw1();
		}
		else if(e.getActionCommand().equals("Medium")){
			level=1;
			GameScreen.levels[level]=true;
			MasterClass.draw1();
		}
		else if(e.getActionCommand().equals("Hard")){
			level=2;
			GameScreen.levels[level]=true;
			MasterClass.draw1();
		}
		else if(e.getActionCommand().equals("Instructions")){
			MasterClass.cards.show(MasterClass.ch,"Instructions");
		}
		else if(e.getActionCommand().equals("Store")){
			MasterClass.cards.show(MasterClass.ch, "Store");
		}
	}
	public void paintComponent(Graphics g){
		super.paintComponent(g);
		g.drawImage(background,0,0,600,500,this);//draw image
	}
}



/* Carol Wang

   This is the class with the good food array and makes each good food
object do its job.

 */

import java.util.*;
import java.awt.*;

public class GoodFoodManager {
	
	public ArrayList<GoodFood> goodfoodarray=new ArrayList<GoodFood>();//image array list with good food images objects
	public int imageindex;
	public GameScreen gs;

	GoodFoodManager(GameScreen _gs){
		gs=_gs;
	}

	public void update(){
		for(int i=goodfoodarray.size()-1;i>=0;i--){
			goodfoodarray.get(i).update();
		}
	}

	public void generateRandomly(){
		GoodFood gf=new GoodFood((int)(Math.random()*555+10),-20,(int)(Math.random()*4),this,gs);//generate one object at random location and generate different image by image index
		goodfoodarray.add(gf);//add it to arraylist
	}

}


/*Carol Wang
  This is the main game screen with the panda, food, trash, and background.
 */
import java.awt.*;
import java.awt.event.*;

import javax.swing.*;

public class GameScreen extends JPanel implements KeyListener, MouseListener{//implement key and mouse listener to move panda
    boolean upgradedalready;//variable to see if level has already been raised so it won't be raised twice
    //global variable for time
    int x=0;
    //see how long shield activated
    int time=0;
    // global variable to see if game is over
    public static boolean gameover=false;
    //to see if 15 seconds was reached
    public boolean timeup;
    //global variables for classes
    public GoodFoodManager gf;
    public BadFoodManager bf;
    public ShieldManager sm;
    public CoinManager cm;
    public Panda panda=new Panda();
    public Bunny bunny=new Bunny();
    public Bear bear=new Bear();
    public Penguin penguin=new Penguin();
    public int score=0;//keep track of score
    public Image background;//background
    // global variables array to see which level it is
    public static boolean[] levels=new boolean[3];
    public static boolean p;//sees if level advanced
    static boolean shieldactivated;
    
    GameScreen(){
        background=Toolkit.getDefaultToolkit().getImage("hungrypanda/bamboo.jpg");
        bf=new BadFoodManager(this);
        gf=new GoodFoodManager(this);
        cm=new CoinManager(this);
        sm=new ShieldManager(this);
        addMouseListener(this);
        addKeyListener(this);
    }


    public void drawAndUpdate(){//responsible for generating and painting and updating continuously
       while(x<=150){//while timer is above zero seconds
            if(score==100&&levels[2]){//to see if user reached max
                upgradedalready=true;
                gameover=true;
            }
            if(gameover){//to show gameover screen
                break;
            }
            else if(score==100&&!upgradedalready){//see if user can go up a level
                p=true;
            	upgradedalready=true;
                levels[InitialScreen.level]=false;
                InitialScreen.level+=1;
                levels[InitialScreen.level]=true;
                break;
            }
            if(shieldactivated){
            	time++;
            	if(time>=100){
            		shieldactivated=false;
            		time=0;
            	}
            }
            if(Math.random()>.80){//randomly generate the objects
                gf.generateRandomly();
                bf.generateRandomly();
            }
            else if(Math.random()>.93){
            	cm.generateRandomly();
            }
            else if(Math.random()>.98&&!shieldactivated){
            	sm.generateRandomly();
            }
            //update the objects
            gf.update();
            bf.update();
            cm.update();
            sm.update();
            //call repaint to draw all classes
            repaint();
            try{//pause for 1/10 of a second so screen will not be too fast
                Thread.sleep(100);
            }catch(InterruptedException ex){}
            x++;//increment time counting variable
        }
        shieldactivated=false;
        x=0;//set back to zero for next time
        if(!gameover&&!p){
            MasterClass.draw2();//only draw questions if game not over
        }
        else{
        	p=false;
            MasterClass.draw1();
        }
    }

    public void mousePressed(MouseEvent e){}
    public void mouseClicked(MouseEvent e){}
    public void mouseReleased(MouseEvent e){}
    public void mouseEntered(MouseEvent e){
        requestFocus();
    }
    public void mouseExited(MouseEvent e){}
    public void keyPressed(KeyEvent e){

        if(e.getKeyCode()==e.VK_LEFT){//if user pressed left key
        	if(Store.currentcharacter==0)
        		panda.updatePosition(true);
        	else if(Store.currentcharacter==1)
        		bear.updatePosition(true);
        	else if(Store.currentcharacter==2)
        		penguin.updatePosition(true);
        	else if(Store.currentcharacter==3)
        		bunny.updatePosition(true);
        }

        else if(e.getKeyCode()==e.VK_RIGHT){//if user pressed right key
        	if(Store.currentcharacter==0)
        		panda.updatePosition(false);
        	else if(Store.currentcharacter==1)
        		bear.updatePosition(false);
        	else if(Store.currentcharacter==2)
        		penguin.updatePosition(false);
        	else if(Store.currentcharacter==3)
        		bunny.updatePosition(false);

        }
        repaint();
    }
    public void keyReleased(KeyEvent e){}
    public void keyTyped(KeyEvent e){}
    public void paintComponent(Graphics g){
        super.paintComponent(g);
        g.drawImage(background, 0,0,600,500,this);
        if(Store.currentcharacter==0)
        	g.drawImage(panda.image,panda.x-48,panda.y-42,panda.width,panda.height,this);
        else if(Store.currentcharacter==1)
        	g.drawImage(bear.image,bear.x-48,bear.y-42,bear.width,bear.height,this);
        else if(Store.currentcharacter==2)
        	g.drawImage(penguin.image,penguin.x-40,penguin.y-56,penguin.width,penguin.height,this);
        else if(Store.currentcharacter==3)
        	g.drawImage(bunny.image,bunny.x-40,bunny.y-56,bunny.width,bunny.height,this);
        //paint panda
        /*g.setColor(Color.WHITE);
        g.fillRoundRect(panda.x-28,panda.y+16,70,60,10,10);//body
        g.fillRoundRect(panda.x-17,panda.y-29,49,45,15,15);//face
        g.setColor(Color.BLACK);
        g.drawRoundRect(panda.x-28,panda.y+16,70,60,10,10);//body
        g.drawRoundRect(panda.x-17,panda.y-29,49,45,15,15);//face
        g.fillRect(panda.x+17,panda.y+76,12,12);//feet p
        g.fillRect(panda.x-11,panda.y+76,12,12);//feet
        g.fillOval(panda.x-41,panda.y+29,15,15);//arm
        g.fillOval(panda.x+40,panda.y+29,15,15);//arm
        g.fillArc(panda.x-16,panda.y-39,16,23,0,185);//ears
        g.fillArc(panda.x+15,panda.y-39,16,23,180,-185);//ears
        g.fillOval(panda.x-8,panda.y-20,8,8);//eyes
        g.fillOval(panda.x+14,panda.y-20,8,8);//eyes
        g.fillOval(panda.x,panda.y,16,10);//mouth
        int[] x1=new int[]{panda.x+4,panda.x+12,panda.x+8};
        int[] y1=new int[]{panda.y-9,panda.y-9,panda.y-3};
        g.fillPolygon(x1,y1,3);//nose
        g.fillOval(panda.x-16,panda.y+23,46,46);//belly*/
        if(shieldactivated&&Store.currentcharacter==0){
        	g.setColor(Color.YELLOW);
        	g.drawOval(panda.x-panda.width/2-5, panda.y-46, panda.width+10, panda.height+5);
        }
        else if(shieldactivated&&Store.currentcharacter==1){
        	g.setColor(Color.YELLOW);
        	g.drawOval(bear.x-bear.width/2-5, bear.y-50, bear.width+10, bear.height+9);
        }
        else if(shieldactivated&&Store.currentcharacter==2){
        	g.setColor(Color.YELLOW);
        	g.drawOval(penguin.x-penguin.width/2+5, penguin.y-60, penguin.width+10, penguin.height+5);
        }
        else if(shieldactivated&&Store.currentcharacter==3){
        	g.setColor(Color.YELLOW);
        	g.drawOval(bunny.x-penguin.width/2+5, bunny.y-60, bunny.width+10, bunny.height+5);
        }
        for(int i=0;i<gf.goodfoodarray.size();i++){     //draw good food
            g.drawImage(gf.goodfoodarray.get(i).image,gf.goodfoodarray.get(i).x,gf.goodfoodarray.get(i).y,gf.goodfoodarray.get(i).width,gf.goodfoodarray.get(i).height,this);
        }
        for(int i=0;i<cm.coinarray.size();i++){
        	g.drawImage(cm.coinarray.get(i).image,cm.coinarray.get(i).x,cm.coinarray.get(i).y,cm.coinarray.get(i).width,cm.coinarray.get(i).height,this);
        }
        for(int i=0;i<sm.shieldarray.size();i++){
        	g.drawImage(sm.shieldarray.get(i).image,sm.shieldarray.get(i).x ,sm.shieldarray.get(i).y, sm.shieldarray.get(i).width,sm.shieldarray.get(i).height,this);
        	
        }
        for(int i=0;i<bf.badfoodarray.size();i++){      //draw bad food
            g.drawImage(bf.badfoodarray.get(i).image,bf.badfoodarray.get(i).x,bf.badfoodarray.get(i).y,bf.badfoodarray.get(i).width,bf.badfoodarray.get(i).height,this);
        }
        g.drawRect(462,0,138,110);
        g.setColor(Color.WHITE);//box to make score and time clear
        g.fillRect(462,0,138,110);
        g.setColor(Color.RED);
        g.setFont(new Font("SanSerif",Font.BOLD,20));
        g.drawString("Score: "+score,482,25);//show score on top
        g.drawString("Coins: "+Store.coincount,230 ,25);
        g.setFont(new Font("SanSerif",Font.BOLD,18));
        g.drawString("Time Left: "+(15-(x/10)),464,108);//show time left
        g.setColor(Color.BLACK);
        g.drawRect(511, 30, 40, 60);//show progress bar for time left on right depending on timer time left
        g.setColor(Color.RED);
        g.fillRect(511, 30, 40, 60);//show full progress bar for time left on right depending on timer time left
        g.setColor(Color.GREEN);
        g.fillRect(511, 30+(4*x/10),40,60-(4*x/10));//shows how much time left
   

    }

}



import java.awt.Image;
import java.awt.Toolkit;

/*Carol Wang
  6-9-14
  This is for the bear character.
 */
public class Bear {
	//global variables for position and size
	public int x=293;//mouth left
	public final int y;
	public int width;
	public int height;
	public Image image;
	
	Bear(){
		image=Toolkit.getDefaultToolkit().getImage("hungrypanda/Bear.png");
		y=415;//mouth top
		width=96;
		height=126;
	}
	public void updatePosition(boolean left){//to see which key was pressed
		if(left){
			if(x-48<=0){//stop panda if it reached the left wall so x of left arm is at zero
				x=48;
			}
			else{//update position
				x-=10;
			}
		}
		else{
			if(x+49>=600){//stop panda if it reached the right wall so right corner of right arm is at 600
				x=551;
			}
			else{//update position
				x+=10;
			}
		}
	}
	public boolean collision(int _x, int _y, int _width, int _height){//see if collision with food objects happened
		boolean yes;
		if(((_x+_width/2)<(x+14)&&(_x+_width/2)>(x-7))&&((_y+_height/2)>=y-5&&(_y+_height/2)<=y+8)){
			yes=true;
		}
		else{
			yes=false;
		}
		return yes;
	}
}


import java.awt.*;

/*Carol Wang
  This is the class with the bad food object.
 */

public class BadFood{
    //own attributes
    public int x;//left
    public int y;//top
    public int width;
    public int height;
    public int speed;
    public Image image;//image
    //classes
    public BadFoodManager bf;
    public GameScreen gs;
    BadFood(int _x, int _y, int imageindex, BadFoodManager _bf, GameScreen _gs){
        gs=_gs;
        bf=_bf;
        x=_x;
        y=_y;
        //load images
        if(imageindex==0){
        	width=40;
        	height=40;
            image=Toolkit.getDefaultToolkit().getImage("hungrypanda/tv.jpg");
        }
        else if(imageindex==1){
        	width=50;
        	height=50;
            image=Toolkit.getDefaultToolkit().getImage("hungrypanda/poop.png");
        }
        else if(imageindex==2){
        	width=40;
        	height=40;
            image=Toolkit.getDefaultToolkit().getImage("hungrypanda/sock.jpg");
        }
        else{
        	width=40;
        	height=40;
            image=Toolkit.getDefaultToolkit().getImage("hungrypanda/paperball.png");
        }

        //depending on levels speed changes
        if(GameScreen.levels[0]){
            speed=10;
        }
        else if(GameScreen.levels[1]){
            speed=15;
        }
        else{
            speed=20;
        }
    }

    public void update(){//update position
        y=y+speed;//speed going down
        if(y>=500){//if past screen
            bf.badfoodarray.remove(this);//remove from array list
        }
        else if(gs.shieldactivated&&Store.currentcharacter==0&&x+width/2>=gs.panda.x-gs.panda.width/2-5&&x+width/2<=gs.panda.x+gs.panda.width/2+10&&y+height-5>=gs.panda.y-46){
            bf.badfoodarray.remove(this);//remove from array list
        }
        else if(gs.shieldactivated&&Store.currentcharacter==1&&x+width/2>=gs.bear.x-gs.bear.width/2-5&&x+width/2<=gs.bear.x+gs.bear.width/2+10&&y+height-5>=gs.bear.y-50){
            bf.badfoodarray.remove(this);//remove from array list
        }
        else if(gs.shieldactivated&&Store.currentcharacter==2&&x+width/2>=gs.penguin.x-gs.penguin.width/2+5&&x+width/2<=gs.penguin.x+gs.penguin.width/2+13&&y+height-5>=gs.penguin.y-60){
            bf.badfoodarray.remove(this);//remove from array list
        }
        else if(gs.shieldactivated&&Store.currentcharacter==3&&x+width/2>=gs.bunny.x-gs.bunny.width/2+5&&x+width/2<=gs.bunny.x+gs.bunny.width/2+13&&y+height-5>=gs.bunny.y-60){
            bf.badfoodarray.remove(this);//remove from array list
        }
        else if(Store.currentcharacter==0){
        	if(gs.panda.collision(x,y,width, height)){
        		bf.badfoodarray.remove(this);//remove from array list
        		GameScreen.gameover=true;
        		gs.drawAndUpdate();
        	}
        }
        else if(Store.currentcharacter==1){
        	if(gs.bear.collision(x,y,width, height)){
        		bf.badfoodarray.remove(this);//remove from array list
        		GameScreen.gameover=true;
        		gs.drawAndUpdate();
        	}
        }
        else if(Store.currentcharacter==2){
        	if(gs.penguin.collision(x,y,width, height)){
        		bf.badfoodarray.remove(this);//remove from array list
        		GameScreen.gameover=true;
        		gs.drawAndUpdate();
        	}
        }
        else if(Store.currentcharacter==3){
        	if(gs.bunny.collision(x,y,width, height)){
        		bf.badfoodarray.remove(this);//remove from array list
        		GameScreen.gameover=true;
        		gs.drawAndUpdate();
        	}
        }
    }
}




/* Carol Wang
   This is the class with the bad food array that makes each bad food
object do its job.
*/
import java.util.*;

public class BadFoodManager{

    public ArrayList<BadFood> badfoodarray=new ArrayList<BadFood>();//image array list with bad food images objects
    public int imageindex;
    public GameScreen gs;

    BadFoodManager( GameScreen _gs){
        gs=_gs;
    }
    public void update(){//update all bad food objects in the array
        for(int i=badfoodarray.size()-1;i>=0;i--){
                badfoodarray.get(i).update();
        }
    }
    public void generateRandomly(){
        BadFood bf=new BadFood((int)(Math.random()*555+10),-20,(int)(Math.random()*4),this,gs);//generate random object with position and which image
        badfoodarray.add(bf);//add to array list
    }

}




import java.awt.Image;
import java.awt.Toolkit;

/*Carol Wang
  This is a panda that eats the food and is controlled by the arrow keys.
 */

public class Panda{
	//global variables for position and size
	public int x=295;//mouth left
	//width of mouth is 14
	//height is 4
	public final int y;
	public int width;
	public int height;
	public Image image;
	Panda(){
		image=Toolkit.getDefaultToolkit().getImage("hungrypanda/Panda.png");
		y=420;//mouth top
		width=96;
		height=122;
	}
	public void updatePosition(boolean left){//to see which key was pressed
		if(left){
			if(x-45<=0){//stop panda if it reached the left wall so x of left arm is at zero
				x=45;
			}
			else{//update position
				x-=10;
			}
		}
		else{
			if(x+48>=600){//stop panda if it reached the right wall so right corner of right arm is at 600
				x=552;
			}
			else{//update position
				x+=10;
			}
		}
	}
	public boolean collision(int _x, int _y, int _width, int _height){//see if collision with food objects happened
		boolean yes;
		if(((_x+_width/2)<=(x+14)&&(_x+_width/2)>=(x-8))&&((_y+_height/2)>=y-5&&(_y+_height/2)<=y+8)){
			yes=true;
		}
		else{
			yes=false;
		}
		return yes;
	}
}






import java.awt.Image;
import java.awt.Toolkit;

/*Carol Wang
  6-9-14
  This is for the penguin character.
 */
public class Penguin{
	//global variables for position and size
	public int x=292;//mouth left
	//mouth width is 16, height is 10
	public final int y;
	public int width;
	public int height;
	public Image image;
	
	Penguin(){
		image=Toolkit.getDefaultToolkit().getImage("hungrypanda/Penguin.png");
		y=445;//mouth top
		width=96;                                               
		height=118;
	}
	public void updatePosition(boolean left){//to see which key was pressed
		//System.out.println(x);
		//System.out.println(y);
		
		if(left){
			if(x-39<=0){//stop panda if it reached the left wall so x of left arm is at zero
				x=39;
			}
			else{//update position
				x-=10;
			}
		}
		else{
			if(x+56>=600){//stop panda if it reached the right wall so right corner of right arm is at 600
				x=544;
			}
			else{//update position
				x+=10;
			}
		}
	}
	public boolean collision(int _x, int _y, int _width, int _height){//see if collision with food objects happened
		boolean yes;
		if(((_x+_width/2)<=(x+18)&&(_x+_width/2)>=(x-8))&&((_y+_height/2)>=y-5&&(_y+_height/2)<=y+10)){
			yes=true;
		}
		else{
			yes=false;
		}
		return yes;
	}
}





/*Carol Wang
  This is the screen that shows the questions.
 */
import java.awt.*;
import java.awt.event.*;
import java.security.acl.Group;
import javax.swing.*;
import java.util.*;

public class QuestionScreen extends JPanel implements ActionListener{
	//array to keep track of how many questions are wrong
	public static ArrayList<Integer> wrong=new ArrayList<Integer>();
    //global variables
    public static int index;
    public static boolean correct;
    //see if any questions were answered wrong
    public static boolean wronganswer;
    //button group
    public static ButtonGroup group;
    //two buttons
    public JRadioButton b1;
    public JRadioButton b2;
    //label for question
    public JLabel label1;
    public JLabel label2;
    public static boolean answered;
    String[] questions=new String[]{"Are there 2 sharps in a D major scale?","Are there 3 sharps in a G major scale?",
    "Are there 7 sharps in a C# major scale?","Are there 5 sharps in an E major scale?","Are there 6 sharps in a F# major scale?",
    "Are there 2 sharps in a G major scale?","Are there 0 sharps in a C major scale?","Are there 5 flats in a Gb major scale?",
    "Is there 1 flat in a F major scale?","Are there 2 flats in an A minor scale?", "Are there 6 flats in a G# minor scale?",
    "Are there 7 flats in an Ab minor scale?","Are there 2 sharps in a F# minor scale?", "Are there 5 flats in a Bb minor scale?",
    "Are there 4 flats in a Db major scale?","Are there 2 sharps in a B minor scale?","Are there 3 flats in a G minor scale?",
    "Are there 6 flats in an Eb minor scale?","Are there 4 sharps in a G# minor scale?", "Are there 4 flats in a F minor scale?",
    "Are there 4 sharps in an E major scale?", "Are there 4 sharps in a F# minor scale?","Are there 4 sharps in a C# minor scale?",
    "Are there 6 sharps in an A# minor scale?","Are there 7 sharps in a C# major scale?","Are there 2 flats in an Eb major scale?",
    "Are there 2 flats in a G minor scale?","Is there a C# in an E minor scale?", "Is there a G# in a F# major scale?",
    "Is there an A# in an E major scale?", "Is there an A# in a B major scale?", "Is there a Db in a F minor scale?", "Is there a B# in a D# minor scale?",
    "Is there an Eb in a Db major scale?", "Is there a Eb in a Bb major scale?", "Is there a Cb in an Ab minor scale?",
    "Is there an E# in a G# minor scale?", "Is there a Gb in a Db major scale?", "Is there a D# in a F# minor scale?",
    "Is there a Fb and a Cb in an Ab minor scale?", "Is there an Eb and an Ab in a G minor scale?", "Is there an A# and a D# in a B major scale?",
    "Is there a Gb and a Cb in a Db major scale?", "Is there a Bb and Db in an Ab major scale?", "Is there a F# and a G# in a B minor scale?",
    "Is there an E# and an A# in a F# major scale?","Is there a Cb in a Bb minor scale?","Is there a Bb and a Cb in an Eb minor scale?", "Is the relative major of an A# minor scale a C# major?",
    "Is the relative minor scale of G major an E minor scale?","Is the relative major of a G# minor an A major scale?", "Is the relative minor of a Db major scale a Bb minor scale?", 
    "Is the relative major of an F minor scale an Eb major scale?", "Is the relative minor of an A major scale a F# minor scale?","Is the relative minor of a D major scale an E minor scale?", 
    "Is the relative major of a B minor scale a D major scale?","Is the relative major of a C minor scale a Bb major scale?", "Is the relative minor of a Gb major scale an Eb minor scale?",
    "Is the relative major of an Ab minor scale a C major scale?", "Is the relative minor of a F# major scale a D# minor scale?", "Is the relative major of a G# minor scale an E major scale?"}; //question array
    //http://www.piano-keyboard-guide.com/images/key_signatures_chart.gif provided me with a chart of the different key signatures to come up with the questions
    String[] answer=new String[]{"true","false","true","false","true","false","true","false","true", "false","true","false","true","false","true","false","true","false","true","false","true","false",
    "true","false","true","false","true","false","true","false","true","false","true","false","true", "false","true","false","true","false","true","false","true","false","true","false","true","false",
    "true","false","true","false","true","false","true","false","true","false","true","false"};//corresponding answer array with answers of either b1 or b2
    QuestionScreen(){
        setLayout(new FlowLayout(FlowLayout.CENTER,700,50));//set flow layout to center and large vertical gap
        //instantiate label
        label2=new JLabel();
        add(label2);
        label1=new JLabel();
        add(label1);//add to panel
        group=new ButtonGroup();//declare button group
        b1=new JRadioButton("true");//true button
        b2=new JRadioButton("false");//false button
        //add them to button group
        group.add(b1);
        group.add(b2);
        //add them to screen
        add(b1);
        add(b2);
        //add action listener
        b1.addActionListener(this);
        b2.addActionListener(this);
    }

    public void paintComponent(Graphics g){
        super.paintComponent(g);
    }

    public void draw(){
        index=(int)(Math.random()*60);
        label1.setFont(new Font("SanSerif",Font.BOLD,16));
        label2.setText("Number of Questions Wrong: "+wrong.size());
        label1.setForeground(new Color((int)(Math.random()*256),(int)(Math.random()*256),(int)(Math.random()*256)));//set random color 
        label1.setText(questions[index]);//show random question by showing index in the question array
    }
    
    public void actionPerformed(ActionEvent e){
        String entered=e.getActionCommand();
        if(entered.equals(answer[index])){
            correct=true;//correct is true
            answered=true;
        }
        else{
            correct=false;//incorrect is true
            answered=true;
        }
        MasterClass.draw1();//call draw in Master class 
    }
}



/*Carol Wang
  6-9-14
  This is for the shield object.
 */
import java.awt.Image;
import java.awt.Toolkit;

/*Carol Wang
  This is the class with the bad food object.
 */

public class Shield{
    //own attributes
    public int x;//left
    public int y;//top
    public final int width=40;
    public final int height=40;
    public int speed;
    public Image image;//image
    //classes
    public ShieldManager sm;
    public GameScreen gs;
    Shield(int _x, int _y, int imageindex, ShieldManager _sm, GameScreen _gs){
        gs=_gs;
        sm=_sm;
        x=_x;
        y=_y;
        //load images
        image=Toolkit.getDefaultToolkit().getImage("hungrypanda/shield.jpg");
       
        //depending on levels speed changes
        if(GameScreen.levels[0]){
            speed=10;
        }
        else if(GameScreen.levels[1]){
            speed=15;
        }
        else{
            speed=20;
        }
    }

    public void update(){//update position
        y=y+speed;//speed going down
        if(y>=500){//if past screen
            sm.shieldarray.remove(this);//remove from array list
        }
        else if(Store.currentcharacter==0){
			if(gs.panda.collision(x,y,width, height)){
				sm.shieldarray.remove(this);//remove from array list
				GameScreen.shieldactivated=true;
			}
		}
		else if(Store.currentcharacter==1){
			if(gs.bear.collision(x,y,width, height)){
				sm.shieldarray.remove(this);//remove from array list
				Store.coincount+=1;
				GameScreen.shieldactivated=true;
			}
		}
		else if(Store.currentcharacter==2){
			if(gs.penguin.collision(x,y,width, height)){
				sm.shieldarray.remove(this);//remove from array list
				Store.coincount+=1;
				GameScreen.shieldactivated=true;
			}
		}
		else if(Store.currentcharacter==3){
			if(gs.bunny.collision(x,y,width, height)){
				sm.shieldarray.remove(this);//remove from array list
				Store.coincount+=1;
				GameScreen.shieldactivated=true;
			}
		}
    }
}





import java.util.*;

/*Carol Wang
  6-9-14
  This is for keeping track of the shields.
 */
public class ShieldManager {

	public ArrayList<Shield> shieldarray=new ArrayList<Shield>();//image array list with bad food images objects
	public int imageindex;
	public GameScreen gs;

	ShieldManager(GameScreen _gs){
		gs=_gs;
	}
	public void update(){//update all bad food objects in the array
		for(int i=shieldarray.size()-1;i>=0;i--){
			shieldarray.get(i).update();
		}
	}
	public void generateRandomly(){
		Shield bf=new Shield((int)(Math.random()*555+10),-20,(int)(Math.random()*4),this,gs);//generate random object with position and which image
		shieldarray.add(bf);//add to array list
	}

}




/*Carol Wang
  This is the timer class that keeps track of time and shows the  time counting down.
  */

public class Timer{
    Timer(){
    }
    public void count(int miliseconds){//purely for the switch between question and game screen
            try{
                Thread.sleep(miliseconds);//sleep for one second for however many of seconds
            }catch(InterruptedException ex){}
    }
	
}


















