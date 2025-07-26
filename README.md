# Digital-Thermometer-with-Fan-Control-and-LCD-Display
#include<reg51.h> // Include header file for 8051 microcontroller

// Define the pins connected to different components sbit full=P1^0; // Pin connected to full sensor
sbit mid=P1^1; // Pin connected to mid-level sensor sbit emp=P1^2; // Pin connected to empty sensor sbit t2=P1^3;	// Pin connected to tank 2 sensor
sbit rs=P0^0;	// Pin connected to RS (Register Select) pin of LCD sbit rw=P0^1;		 // Pin connected to RW (Read/Write) pin of LCD sbit en=P0^2;		// Pin connected to EN (Enable) pin of LCD
sbit rly=P3^0;  // Pin connected to relay for controlling motor


void lcddta(unsigned char[],unsigned char); // Function to send data to LCD void lcdcmd(unsigned char); // Function to send command to LCD
void msdelay(unsigned int);// Function for software delay void main(void){
rly=0;	 // Initialize relay to off state P0=00;	// Initialize P0 port
P2=00;	// Initialize P2 port full=1;	 // Initialize full sensor pin mid=1;	// Initialize mid sensor pin 



emp=1;		// Initialize empty sensor pin t2=1;	// Initialize tank 2 sensor pin

lcdcmd(0x38); // 2 lines, 5x7 matrix lcdcmd(0x0c); // Display on, cursor off lcdcmd(0x06); // Increment cursor lcdcmd(0x01); // Clear display
while(1){
if(t2==0){ // Check if tank 2 is empty
// lower motor condition
if(emp==1 && mid==1 && full==1){ rly=1; // Turn on motor lcdcmd(0x01);	// Clear display
lcdcmd(0x80);	 // Set cursor to first line lcddta("tank is empty",13); // Display message lcdcmd(0xc0);	// Set cursor to second line lcddta("motor is on",11); // Display message
}else if(emp==0 && mid==1 && full==1){ rly=1; // Turn on motor
lcdcmd(0x01);	 // Clear display lcdcmd(0x80);	 // Set cursor to first line lcddta("tank filling",12); // Display message lcdcmd(0xc0);	// Set cursor to second line lcddta("motor is on",11); // Display message
}else if(emp==0 && mid==0 && full==1){ rly=1; // Turn on motor
lcdcmd(0x01);	 // Clear display lcdcmd(0x80);	 // Set cursor to first line lcddta("tank is mid",11); // Display message lcdcmd(0xc0);	// Set cursor to second line lcddta("motor is on",11); // Display message
}else if(full==0 && mid==0 && emp==0){ rly=0; // Turn off motor
lcdcmd(0x01);	 // Clear display lcdcmd(0x80);	 // Set cursor to first line lcddta("tank is full",12); // Display message lcdcmd(0xc0);	// Set cursor to second line lcddta("motor is off",12); // Display message
}

}else{
rly=0; // Turn off motor for tank 2
lcdcmd(0x01);	// Clear display lcdcmd(0x80);	// Set cursor to first line 



lcddta("tank 2 empty",12); // Display message lcdcmd(0xc0);	// Set cursor to second line lcddta("fill tank 2",11); // Display message
}
} //end of while
}//end of main

void lcdcmd(unsigned char cmd){
P2=cmd;			// Send command to LCD rs=0;	// Set RS to 0 for command mode rw=0;		 // Set RW to 0 for write mode en=1;		// Enable LCD
msdelay(5); // Delay for stabilization en=0;	// Disable LCD
}//end of lcdcmd

void lcddta(unsigned char a[],unsigned char len){ unsigned char x;

for(x=0;x<len;x++){ // Loop through each character in the string P2=a[x];	// Send data to LCD
rs=1;	// Set RS to 1 for data mode rw=0;		 // Set RW to 0 for write mode en=1;		// Enable LCD
msdelay(5);		// Delay for stabilization en=0;	// Disable LCD
}//end of for
}//end of lcddta

void msdelay(unsigned int a)
{
unsigned int x,y; for(x=0;x<a;x++)
for(y=0;y<1275;y++); // Nested loop for delay
}//end of msdelay
