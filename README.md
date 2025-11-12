# mca
7
*/ #include <p18f4520.h>
#include <stdio.h>
#include <delays.h>
#pragma config OSC = HS // High-speed oscillator
#pragma config WDT = OFF // Watchdog Timer disabled
#pragma config LVP = OFF // Low-voltage Programming disabled
void InitUSART()
{
TRISCbits.TRISC6 = 1; //make Tx Pin of UART
TRISCbits.TRISC7 = 1; //make Rx Pin of UART
SPBRG = 31 ; //9600 baud @ 20MHz(HS MODE)
TXSTA = 0x20; //Transmitter Enable
RCSTAbits.SPEN =1; //Serial Port Enble
RCSTAbits.CREN =1; //Continues Receive Enable bit
}

unsigned char getchar(void)
{
unsigned char ch;
while (!(PIR1bits.RCIF==1)); //wait until there's a character to be read ch=RCREG;
//then read from the Receiver Buffer Register return ch;
}
unsigned char putchar(unsigned char ch)
{
while (!(PIR1bits.TXIF==1)); //wait until Transmitter Buffer Register Empty TXREG=ch;
//then write to the Transmitter Buffer Register return ch;
}
void main (void)
{
InitUSART() ;
printf("Hello PIC18f4520!\r\n") ;
while(1)
{
putchar(getchar());
}
}


6\\\
#include <p18f4520.h>
#include <delays.h>

#pragma config OSC = HS // High-speed oscillator
#pragma config WDT = OFF // Watchdog Timer disabled
#pragma config LVP = OFF // Low-voltage Programming disabled
#pragma config PBADEN = OFF
#define BUZZER PORTAbits.RA3 //Buzzer connected to PORTA 3rd PIN
unsigned char TimerOnflag=0, Num=0;
void Timer0Init()
{
T0CON = 0x87; ; // Prescaler of 256, Timer Prescaler assigned , Increment on low to high transition,
//16 BIT MODE, Turns timer on
TMR0H =0xB3;
TMR0L = 0xB4;
// Interrupt Setup
RCONbits.IPEN = 1; // Interrupt priority enabled
INTCON = 0xE0; // Enables Global Interrupts, Peripheral Interrupts, Enables overflow Interrupt, Sets
//overflow bit to zero
INTCON2bits.TMR0IP = 1; // Timer 0 Interrupt is high priority
}
void TMRISR (void) ;
#pragma code InterruptVectorHigh=0x08
void InterruptVectorHigh (void)
{
_asm
goto TMRISR
_endasm
}
#pragma code
#pragma interrupt TMRISR
void TMRISR (void) //Timer ISR
{
if(INTCONbits.TMR0IF == 1)
{
INTCONbits.TMR0IF=0; //Clear Interrupt Flag to prevent reinitialisation of ISR
TMR0H =0xB3;
TMR0L = 0xB4;
if(TimerOnflag)
{
PORTD = 0x00;
TimerOnflag=0;
BUZZER =0;
Delay10KTCYx(700);
}
else
{
PORTD = 0xff;
TimerOnflag=1;
BUZZER =0;
Delay10KTCYx(700);
}
}
}
void main (void)
{
Timer0Init();
TimerOnflag=0;
TRISD = 0x00; //Port configured as Output
PORTD = 0x00; //PORTD = 0x00
while(1)
{
BUZZER =1;
}
}




/////1 
