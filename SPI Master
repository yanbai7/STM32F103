#include "sbus.h"

#define SPI1_NSS	GPIO_Pin_4
#define SPI1_SCK	GPIO_Pin_5
#define SPI1_MOSI	GPIO_Pin_7
#define SPI1_MISO	GPIO_Pin_6

#define ENABLE_CS			GPIO_ResetBits(GPIOA,SPI1_NSS); 
#define DISABLE_CS		GPIO_SetBits(GPIOA,SPI1_NSS); 

void initSpiMaster(void)
{
	GPIO_InitTypeDef GPIO_InitStructure;
	SPI_InitTypeDef  SPI_InitStructure;

	RCC_APB2PeriphClockCmd(	RCC_APB2Periph_SPI1,  ENABLE ); //Enable SPI1 Clock
	
	SPI_InitStructure.SPI_Direction = SPI_Direction_2Lines_FullDuplex;  //Full Duplex Mode
	SPI_InitStructure.SPI_Mode = SPI_Mode_Master;												//Master
	SPI_InitStructure.SPI_DataSize = SPI_DataSize_16b;									//16 Data bits
	SPI_InitStructure.SPI_CPOL = SPI_CPOL_Low;													//Stable Level is Low
	SPI_InitStructure.SPI_CPHA = SPI_CPHA_2Edge;												//Falling Edge
	
	//SpeedSet:
	//SPI_BaudRatePrescaler_2   (SPI 36M@sys 72M)
	//SPI_BaudRatePrescaler_8   (SPI 9M@sys 72M)
	//SPI_BaudRatePrescaler_16  (SPI 4.5M@sys 72M)
	//SPI_BaudRatePrescaler_256 (SPI 281.25K@sys 72M)
  
	SPI_InitStructure.SPI_NSS = SPI_NSS_Soft;															
	SPI_InitStructure.SPI_BaudRatePrescaler = SPI_BaudRatePrescaler_16;		//SPI Baudrate = 4.5MHz
	SPI_InitStructure.SPI_FirstBit = SPI_FirstBit_MSB;										//Transmit from MSB to LSB
	SPI_Init(SPI1, &SPI_InitStructure);  																	
	SPI_Cmd(SPI1, ENABLE); 
	
	
	// RM0008 STM32 Reference Manual "Table 25. SPI"
	RCC_APB2PeriphClockCmd(	RCC_APB2Periph_GPIOA, ENABLE );	//Enable PORTA Clock
	GPIO_InitStructure.GPIO_Pin = SPI1_SCK|SPI1_MOSI;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP; 	
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);
	
	GPIO_InitStructure.GPIO_Pin = SPI1_MISO;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING; 
	GPIO_Init(GPIOA, &GPIO_InitStructure);

	GPIO_InitStructure.GPIO_Pin = SPI1_NSS;			
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; 
	GPIO_Init(GPIOA, &GPIO_InitStructure);
	GPIO_SetBits(GPIOA,SPI1_NSS); 

}   

uint16_t sendSpiData(uint16_t data)
{
	uint16_t dummy = 0;
	
	ENABLE_CS
	
	while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_TXE) == RESET);
	SPI_I2S_SendData(SPI1, data);
	
	while (SPI_I2S_GetFlagStatus(SPI1, SPI_I2S_FLAG_RXNE) == RESET);
	dummy = SPI_I2S_ReceiveData(SPI1);
	
	DISABLE_CS
	
	return dummy;
}
