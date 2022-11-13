#include <reg51.h>
#include <intrins.h>

#define op P3

void delay(unsigned int a);
void Delay_servo(unsigned int);
void convert(unsigned int value);

void open();
void close();
sbit IR1=P0^0;
sbit IR2=P0^1;
sbit control_pin=P2^0;
sbit rs=P3^0;
sbit en=P3^1;
sbit B1=P0^2;
sbit B2=P0^3;
void lcd_init();
void lcd_cmd(unsigned char );
void lcd_data(unsigned char );
void lcd_delay();
void lcd_str(char*);

int space=5;

void main(){
	

	lcd_init();
	lcd_cmd(0x01); //clear screen 
	lcd_str("AUTOMATIC CAR ");
	lcd_cmd(0xc0);   //display ON
	lcd_str("PARKING SYSTEM");
	lcd_init();
	lcd_str("Total slots:5");
	lcd_cmd(0xc0); // cursor to 2nd line
	lcd_str("EMPTY SLOTS:5");
  lcd_cmd(0x01);
	lcd_init();
	
while(1){	
if(IR1==1){  						//IR SENSOR1
	
	lcd_init();
	lcd_cmd(0x01); //clear screen 
	lcd_str("GATE OPENING ");
	lcd_cmd(0xc0);   //display ON
	lcd_str("WELCOME :) ");
	
open();
delay(200);
	
	lcd_cmd(0x01); //clear screen 
	lcd_str("GATE CLOSING");
	lcd_cmd(0xc0);   //display ON
	
close();
delay(200);

}
else if(IR2==1){				//IR SENSOR 2
	
	lcd_init();
	lcd_cmd(0x01); //clear screen 
	lcd_str("GATE OPENING ");
	lcd_cmd(0xc0);   //display ON
	lcd_str(":) ");
	
	open();
	delay(200);
	
	lcd_cmd(0x01); //clear screen 
	lcd_str("GATE CLOSING ");
	lcd_cmd(0xc0);   //display ON
	
	close();
	delay(200);}

else if(B1==1){						// BUTTON 1
			 lcd_init();
	lcd_cmd(0x01); //clear screen 
	lcd_str("GATE OPENING ");
	lcd_cmd(0xc0);   //display ON
	lcd_str("BY MANUALLY");
open();
		 }

else if(B2==1){						//BUTTON 2
      lcd_cmd(0x01); //clear screen 
	lcd_str("GATE CLOSING ");
	lcd_cmd(0xc0);   //display ON
	lcd_str("BY MANUALLY");
	close();

	}

	
}
}
	
void delay(unsigned int time)  // Function for creating delay in milliseconds.
{
    unsigned i,j ;
    for(i=0;i<time;i++)    
    for(j=0;j<1275;j++);
}
void close(){
						control_pin=0;                                                 // output pin                                             //Turn to 90 degree
            control_pin=1;
            Delay_servo(1354);					//1354 for 0
            control_pin=0;

}
void open(){
						control_pin=0;                                                 // output pin                                             //Turn to 90 degree
            control_pin=1;
            Delay_servo(2018);					//1354 for 0
            control_pin=0;

}
void Delay_servo(unsigned int d)
  {
  TMOD &=0xF0;                    // Clear 4bit field for Timer0
  TMOD|=0x01;                                   // Set timer0 in mode1, 16bit
  TH0=0xFF - (d>>8)&0xFF;              // Load delay vales Timer 0 + Bitwise right shift[c][d]a >> b
  TL0=0xFF- d&0xFF;              
  ET0=1;                                              //Enable timer0 interrupts
  EA=0;                                                // Global Interrupt
  TR0=1;                                              // Start timer 0
  while(TF0==0);                                 // Wait for overflow
  TR0=0;                                  //Stop timer0
  TF0=0;                                              // Clear Flag

  }
	void lcd_delay(int a){ //lcd delay
	unsigned int k,j;
	for(k=0;k<=a;k++)
	for(j=0;j<=4000;j++);
}

void lcd_cmd(char ch){
op=ch&(0xf0);
rs=0;
	en=1;
	lcd_delay(1);
	en=0;
	op=((ch<<4)&(0xf0));
	rs=0;
en=1;
lcd_delay(1);
en=0;
}	

void lcd_data(char ch){
	op=ch&(0xf0);
	rs=1;
	en=1;
	lcd_delay(1);
	en=0;
	op=((ch<<4)&(0xf0));
	rs=1;
	en=1;
	lcd_delay(1);
	en=0;
}

void lcd_init(){
	lcd_cmd(0x01);  // clear display
	lcd_cmd(0x02);  //return home
	lcd_cmd(0x28);
	lcd_cmd(0x28); // 28 for 4bit 83 for 8 bit
	lcd_cmd(0x0e);
	lcd_cmd(0x80);
}
void lcd_str(char *p){     //string function
	while(*p!='\0'){
		lcd_data(*p++);
	}
}


