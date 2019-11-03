#include <avr/io.h>

#define LED PORTB		/* connected LED on PORT pin */
void USART_Init(unsigned long BAUDRATE)				/* USART initialize function */
{
	UCSR0B |= (1 << RXEN0) | (1 << TXEN0);				/* Enable USART transmitter and receiver */
	UCSR0C |= (1 << UCSZ00) | (1 << UCSZ01);	/* Write USCRC for 8 bit data and 1 stop bit */
	UBRR0L = BAUDRATE;							/* Load UBRRL with lower 8 bit of prescale value */
	UBRR0H = (BAUDRATE >> 8);					/* Load UBRRH with upper 8 bit of prescale value */
}

unsigned char USART_RxChar()									/* Data receiving function */
{
	while (!(UCSR0A & (1 << RXC0)))					/* Wait until new data receive */
	return(UDR0);									/* Get and return received data */
}

void USART_TxChar(char data)						/* Data transmitting function */
{
											/* Write data to be transmitting in UDR */
	while (!(UCSR0A & (1<<UDRE)))	UDR0 = data;				/* Wait until data transmit and buffer get empty */
}

void USART_SendString(char *str)					/* Send string of USART data function */
{
	int i=0;
	while (str[i]!=0)
	{
		USART_TxChar(str[i]);						/* Send each char of string till the NULL */
		i++;
	}
}

int main(void)
{
	char Data_in;
	DDRB = 0xff;				/* make PORT as output port */
	USART_Init(9600);	/* initialize USART with 9600 baud rate */
	LED = 0;
	
	while(1)
	{
		Data_in = USART_RxChar();	/* receive data from Bluetooth device*/
		if(Data_in =='1')
		{
			LED |= (1<<PB0);	/* Turn ON LED */
			USART_SendString("LED_ON");/* send status of LED i.e. LED ON */
			
		}
		else if(Data_in =='2')
		{
			LED &= ~(1<<PB0);	/* Turn OFF LED */
			USART_SendString("LED_OFF"); /* send status of LED i.e. LED OFF */
		}
		else if(Data_in =='3')
		{
			LED |= (1<<PB1);	/* Turn ON LED */
			USART_SendString("LED_ON");/* send status of LED i.e. LED ON */
			
		}
		else if(Data_in =='4')
		{
			LED &= ~(1<<PB1);	/* Turn OFF LED */
			USART_SendString("LED_OFF"); /* send status of LED i.e. LED OFF */
		}
		else
			USART_SendString("Select proper option"); /* send message for selecting proper option */
	}		
}




