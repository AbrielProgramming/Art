import java.awt.event.*;
import java.awt.*;
import javax.swing.event.MouseInputAdapter;
import javax.swing.*;

import java.awt.geom.*;
public class Scene extends MouseInputAdapter {
    JFrame frame;
    JPanel graphingPane;
    private Timer timer,sunTimer;
    private PointerInfo trial;
    private Point current;
    private  int x,y;
    private int watery=350;
    private int dropPointD=35,factor=1;;
    private int dropx,dropy;
    private boolean dripping=false,splashing=false;
    private int splashFactor1=1,splashFactor2=1,splashFactor3=1;
    private int acceleration=1;
    private int red1=51,green1=255,blue1=255;
    private int red2=255,green2=255,blue2=255;
    private int oceanBlue=255;
    private int sunProgression=1;
    private int birdx=0,birdy=250, wing=0, birdx2=-30,birdy2=200, wing2=30;
    private boolean birdFlight=true, birdFlight2=true, up=false, up2=true;
    private boolean night=false;
    private int nighttime=1;
    private double starsAppearing=0;
    private Star[] stars;
    private boolean starShooting=false;
    private int tailx, taily, headx, heady, endx, endy;
    private Bird bird, bird2;
    public Scene2() {
        frame=new JFrame("Sunset");
        graphingPane=new SecondGraphicsPanel();
        graphingPane.setBackground(Color.white);
        frame.addMouseMotionListener(this);
        frame.addMouseListener(this);
        frame.setContentPane(graphingPane);
        frame.pack();
        frame.setSize(500,500);
        frame.setVisible(true);
        timer = new Timer(20,new SecondListener());
        timer.setInitialDelay(200);
        timer.start();
        sunTimer = new Timer(40,new TimerListener());
        sunTimer.setInitialDelay(200);
        sunTimer.start();
        sunTimer.stop();
        makeStars();
        bird=new Bird(30,birdx,birdy);
        bird2=new Bird(60,birdx,birdy);
    }
    
    public void mouseClicked(MouseEvent e){
        trial=MouseInfo.getPointerInfo();
        current=trial.getLocation();
        x=(int)current.getX()-10;
        y=(int)current.getY()-50;
        if (!dripping){
            dropx=x;
            dropy=y+dropPointD;
        }
        dripping=true;
    }
    private void makeStars(){
        stars=new Star[150];
        int[] starSizes=new int[150];
        int[] starPlacesX=new int[150];
        int[] starPlacesY=new int[150];
        
        for (int counter=0;counter<starSizes.length;counter++){
        	if (counter<=30)
        		starSizes[counter]=(int)(Math.random()*5)+1;
        	else
        		starSizes[counter]=1;
        }
        for (int counter=0;counter<stars.length;counter++){
            starPlacesX[counter]=(int)(Math.random()*500)+1;
            starPlacesY[counter]=(int)(Math.random()*(watery-10))+1;
        }
        for (int counter=0;counter<stars.length;counter++){
            stars[counter]=new Star(starSizes[counter]*2,starSizes[counter],starPlacesX[counter],starPlacesY[counter]);
        }
        
    }
    private void displayStars(Graphics2D g){
        g.setColor(new Color(155,255,255));
        for (int counter=0;counter<starsAppearing;counter++){
            g.fill(stars[counter].getStar());
        }
    }
    private boolean sunUp(){
    	return red1==51&&green1==255&&blue1==255;
    }
    class SecondGraphicsPanel extends JPanel{
        public void paintComponent(Graphics g){
            super.paintComponent(g);
            Graphics2D g2=(Graphics2D)g;
            
            g2.drawLine(0,watery,500,watery);
            GradientPaint bluetowhite=new GradientPaint(100,500,new Color(0,0,oceanBlue),450+sunProgression,watery,Color.white);
            g2.setPaint(bluetowhite);
            g2.fill(new Rectangle(0,watery,500,500-watery));
            bluetowhite=new GradientPaint(250,0,Color.cyan,350,350,Color.white);
            g2.setPaint(bluetowhite);
            g2.fill(new Rectangle(0,0,500,watery));
            GradientPaint sky=new GradientPaint(250,0,new Color(red1,green1,blue1),250,watery+100,new Color(red2,green2,blue2));
            g2.setPaint(sky);
            g2.fill(new Rectangle2D.Double(0,0,500,watery));
            displayStars(g2);
            bluetowhite=new GradientPaint(0,500+sunProgression,Color.white,500,0,new Color(0,0,oceanBlue));
            g2.setPaint(bluetowhite);
            if (dripping&&!splashing){
                GeneralPath drop=new GeneralPath();
                drop.moveTo(dropx, dropy-dropPointD);
                if (dropy>0)
                    drop.lineTo(dropx-dropPointD/3, dropy);
                drop.curveTo(dropx-(13+factor/3),dropy+30,dropx+(13+factor/3),dropy+30,dropx+dropPointD/3,dropy);
                drop.lineTo(dropx, dropy-dropPointD);
                g2.fill(drop);
            }
            if (dropy>=watery&&dripping){
                splashing=true;
                splash(g2);
            }
            if (birdFlight&&sunUp()){
            	g2.setColor(Color.black);
            	bird.setPosition(birdx, birdy,wing);
            	g2.draw(bird.getBird());
            }
            if (birdFlight2&&sunUp()){
            	g2.setColor(Color.black);
            	bird2.setPosition(birdx2,birdy2,wing2);
            	g2.draw(bird2.getBird());
            }
            if (starShooting){
            	g2.setColor(Color.white);
            	g2.draw(starShape(tailx,taily,headx,heady));
            }
        }
    }

    
    private void splash(Graphics2D g){
        acceleration=1;
        dropPointD=35;
        factor=1;
        if (splashing){
            g.drawOval(dropx-25-splashFactor1/2,dropy,50+splashFactor1,30);
            if (splashFactor1>=100)
                g.drawOval(dropx-25-splashFactor2/2,dropy,50+splashFactor2,30);
            if (splashFactor2>=100)
                g.drawOval(dropx-25-splashFactor3/2,dropy,50+splashFactor3,30);
        }
        
    }
    private void flapWings(){
    	if (birdx<500&&birdFlight){
        		birdx+=3;
        		if (birdy>0)
        			birdy--;
        }else{
       		birdx=0;
       		birdFlight=false;
       		birdy=150;
       	}
       	if (birdx2<500&&birdFlight2){
       		birdx2+=3;
       		if (birdy2>0)
       			birdy2--;
        }else{
       		birdx2=-30;
       		birdFlight2=false;
       		birdy2=200;
       	}
       	if (wing<15&&!up){
       		wing++;
       	}else{
       		up=true;
       		if (wing>-15)
       			wing--;
       		else
       			up=false;
       	}
       	if (wing2>-30&&up2){
       		wing2-=2;
       	}else{
       		up2=false;
       		if (wing2<30)
       			wing2+=2;
       		else
       			up2=true;
       	}
    }
    private GeneralPath starShape(int startailx, int startaily, int starheadx, int starheady){
    	GeneralPath shootingStar=new GeneralPath();
    	shootingStar.moveTo(startailx,startaily);
    	shootingStar.curveTo(starheadx, starheady-5, starheadx, starheady+5, startailx, startaily);
    	return shootingStar;
    }
   
    class SecondListener implements ActionListener{
        public void actionPerformed(ActionEvent event){
        	flapWings();		
        	if (starShooting){
        		if (headx<endx)
        			headx+=2;
        		if (heady<endy)
        			heady++;
        		if (headx>=endx&&heady>=endy){
        			if (tailx<endx)
        				tailx+=2;
        			if (taily<endy)
        				taily++;
        			if (tailx>=endx&&taily>=endy)
        				starShooting=false;
        		}
        	}
            if (dropPointD>0&&dripping){
                dropPointD-=2;
                factor++;
            }
            if (dropy<watery&&dripping){
                acceleration++;
                dropy+=acceleration/2;
            }
            if (splashing&&splashFactor1<400){
                splashFactor1+=5;
            }
            if (splashing&&splashFactor1>=100&&splashFactor2<400){
                splashFactor2+=5;
            }
            if (splashing&&splashFactor2>=100&&splashFactor3<400){
                splashFactor3+=5;
            }else if (splashFactor3>=400){
                splashing=false;
                splashFactor1=1;
                splashFactor2=1;
                splashFactor3=1;
                acceleration=1;
                factor=1;
                dripping=false;
                dropPointD=35;
                
            }
            if (sunProgression>=915){
                nighttime++;
                if (nighttime>=250){
                    nighttime=1;
                    sunTimer.restart();
                }
            }
            if (sunProgression<=10){
                nighttime++;
                if (nighttime>=400){
                    nighttime=1;
                    sunTimer.restart();
                }
            }
            graphingPane.repaint();
        }
    }
    class TimerListener implements ActionListener{
        public void actionPerformed(ActionEvent e){
            if (!night){
            	if (oceanBlue>80&&sunProgression>=200)
            		oceanBlue--;
                if (starsAppearing<stars.length)
                    starsAppearing+=.5;
                sunProgression+=2;
                if (red2>250){
                    red2--;
                }    
                if (blue2>80){
                    blue2--;
                }
                if (green1>51){
                    green1--;
                }
                if (green1<=51){
                    if (green1>0)
                        green1--;
                    if (red1>0)
                        red1--;
                    if (blue1>0)
                        blue1--;
                    if (green2>0)
                        green2--;
                    if (red2>0)
                        red2--;
                    if (blue2>0)
                        blue2--;
                    if (red1==0&&blue1==0&&green1==0&&red2==0&&blue2==0&&green2==0){
                        night=true;
                        sunTimer.stop();
                        starShooting=true;
                        tailx=(int)(Math.random()*450) + 50;
                        taily=(int)(Math.random()*(watery-100)) + 50;
                        endx=tailx+60;
                        endy=taily+30;
                        headx=tailx+1;
                        heady=taily+1;
                    }
                }
            }

            if (night){
            	if (oceanBlue<255)
            		oceanBlue++;
                if (starsAppearing>0)
                    starsAppearing-=.5;
                if (sunProgression>2)
                	sunProgression-=2;
                if (red2<255)
                    red2++;
                if (green2<247)
                    green2++;
                if (blue1<255)
                    blue1++;
                if (green1<34)
                    green1++;
                if (red2>=255&&green2>=247&&blue1>=255&&green1>=34){
                    if (green1<255)
                        green1++;
                    if (red1<51)
                        red1++;
                    if (green2<255)
                        green2++;
                    if (blue2<255)
                        blue2++;
                }
                if (green1==255&&red1==51&&green2==255&&blue2==255){
                    night=false;
                    birdFlight=true;
                    birdFlight2=true;
                    sunTimer.stop();
                }
            }

            graphingPane.repaint();

        }

    }

    private static void createAndShowGUI() {

        JFrame.setDefaultLookAndFeelDecorated(true);

        Scene2 test=new Scene2();

    }
    public static void main(String[] args) {
    	
        javax.swing.SwingUtilities.invokeLater(new Runnable() {

            public void run() {

                createAndShowGUI();

            }

        });

    }
    
}
