#include <16f877.h>
#use delay(clock=20000000)
#use rs232(baud=19200,parity=N,xmit=PIN_C6,rcv=PIN_C7)

#include <math.h>     // utilisation de la bibliothèque de calcul

#define M1 1
#define M2 2  
#define M_12 3
#define AVANT 1
#define ARRIERE 0

#define ARRET_DEUX_MOTEURS    0 
#define MOTEUR_AVANT_1        1
#define MOTEUR_ARRIERE_1      2
#define MOTEUR_AVANT_2        3
#define MOTEUR_ARRIERE_2      4
#define AVANCE_DEUX_MOTEURS   5
#define RECULE_DEUX_MOTEURS   6 

#define WRITING     0
#define READING     1

#define ENREGISTRE  pin_a5 
#define av_serv1  pin_a0
#define av_serv2  pin_e1
#define repeter  pin_a4
#define av_serv12  pin_e0
#define ar_serv12  pin_a2



int1 last_b4=0,last_b5=0; 
signed int16 b4=0,b5=0,derniere_valeur_M1=0,derniere_valeur=0,derniere_valeur_M2=0,valeur=0,valeurD=0,valeurG=0;// derniere valeur c'est pour indique la position où il s'arrete le moteur
int phase = -1,dir_1=0, dir_2=0,v=0,addr_enr=0,addr_enr_2=0, choix_moteur=0,choix_mot_eeprom=0; //pahse: c pour ne pas répéter "!!write!!" plusieurs fois// et dir pour indiquer la direction
int1  last_c3=0,last_a1=0,last_av_serv12=0,last_ar_serv12=0; //les boutons nécaissaire pour avancer ou réculer le moteur
int val=ARRET_DEUX_MOTEURS; //initialisation de l'état du moteur au debut du programme
int a=0,nbre=0,i=0;



//terminer
void maj_drnr_val()
{    
    derniere_valeur_M1+=b4;
    derniere_valeur_M2+=b5;
    b4=0;
    b5=0;
} 


//terminer
#int_rb // Interruption sur rb de b4 à b5
void rd_encoder() // read encodeur rd_encoder
{
   if(input(pin_b4) != last_b4)  //changement de l'etat de l'encodeur b5 et b6
   {      
      if( (last_b4==0 )&& ( input(pin_d3)==1 ) )
      {
          b4--;
          dir_1=-1;
      }
      
      if( (last_b4==0 )&& ( input(pin_d3)==0 ) )
      {
          b4++;
          dir_1=1;
      }
      last_b4 = !last_b4;
   }
   
   if(input(pin_b5) != last_b5)  //changement de l'etat de l'encodeur b5 et b6
   {      
      if( (last_b5==0 )&& ( input(pin_d2)==1 ) )
      {
          b5--;
          dir_2=-1;
      }
      
      if( (last_b5==0 )&& ( input(pin_d2)==0 ) )
      {
          b5++;
          dir_2=1;
      }
      last_b5 = !last_b5;
   }   
}

//a regler
void stop (int16 time) 
{
   int16 i;
   time/=20;
   for(i=0;i<time;i++)
   {
      output_bit(pin_b2,1);  //
      output_bit(pin_b1,1);  //
      delay_us(1460);   ////
      output_bit(pin_b2,0);  //
      output_bit(pin_b1,1);
      delay_us(10);
      output_bit(pin_b2,0);
      output_bit(pin_b1,0);
      delay_us(18530);
   }
     
}

//terminer
void ENREGISTREMENT_EEPROM(int choix_addr,int choix_moteur )
{
          if ((choix_mot_eeprom==M1)||(choix_mot_eeprom==M2))
    {
   if (choix_moteur==M1)
   {
   derniere_valeur=derniere_valeur_M1;
   }   
   if (choix_moteur==M2)
   {
   derniere_valeur=derniere_valeur_M2;
   }
   write_eeprom(choix_addr++,(int8)(choix_moteur));
   write_eeprom(choix_addr++,(int8)(derniere_valeur));
   write_eeprom(choix_addr++,(int8)(derniere_valeur >> 8));
    }
          else if (choix_mot_eeprom==M_12)
      {
      write_eeprom(choix_addr++,(int8)(choix_moteur));
      write_eeprom(choix_addr++,(int8)(derniere_valeur_M1));
      write_eeprom(choix_addr++,(int8)(derniere_valeur_M1 >> 8));
      write_eeprom(choix_addr++,(int8)(derniere_valeur_M2));
      write_eeprom(choix_addr++,(int8)(derniere_valeur_M2 >> 8));
      }
    
}

// terminer
void enregistrer()
{
      if(input(ENREGISTRE)==1)
      {
      printf("enregistrer \r\n ");
      delay_ms(500);
      if ((choix_mot_eeprom==M1)||(choix_mot_eeprom==M2))
      {
      printf("D : %ld \t\t G: %ld \r\n",derniere_valeur_M1,derniere_valeur_M2);
      ENREGISTREMENT_EEPROM(addr_enr,choix_mot_eeprom);
      write_eeprom(0x00,(int8)(addr_enr));
      addr_enr+=3;
      }
      else if (choix_mot_eeprom==M_12)
      {
      printf("D : %ld \t\t G: %ld \r\n",derniere_valeur_M1,derniere_valeur_M2);
      ENREGISTREMENT_EEPROM(addr_enr,choix_mot_eeprom);
      addr_enr+=5;
      write_eeprom(0x00,(int8)(addr_enr));
      
      }
      }    
}

//terminer
void affichage()
{
   printf("B4= %Ld *** dir_1= %d ||| B6= %ld *** dir_2= %d\r\n",b4,dir_1,b5,dir_2);
}


//terminer
void dour(int a)
{
 switch (a)
  {
  case MOTEUR_AVANT_1:
while(input(av_serv1)==1 )
  {
      output_bit(pin_b1,1);
      delay_us(1200);
      output_bit(pin_b1,0);
      delay_us(18800);  
      affichage();
   }
   break;
 
   case MOTEUR_AVANT_2:
while(input(av_serv2)==1 )
  {
      output_bit(pin_b2,1);
      delay_us(1800);
      output_bit(pin_b2,0);
      delay_us(18200);
      affichage();
   }
   break;
   
     case AVANCE_DEUX_MOTEURS :
while(input(av_serv12)==1)
  {
  output_bit(pin_b1,1);  //a0 b2
  output_bit(pin_b2,1);  //a1  b3
  delay_us(1200);   //// 900
  output_bit(pin_b1,1);  //a0  b2
  output_bit(pin_b2,0);  //a1  b3
  delay_us(400);  ///  900
  output_bit(pin_b2,0);  //a0  b2
  output_bit(pin_b1,0);
  delay_us(18400); 
  affichage();
   }
  break;
  
  case RECULE_DEUX_MOTEURS :
while(input(ar_serv12)==1)
  {
  output_bit(pin_b2,1);  //
  output_bit(pin_b1,1);  //
  delay_us(1098);   ////1200
  output_bit(pin_b1,0);  //
  output_bit(pin_b2,1);  //
  delay_us(952);  ///700 
  output_bit(pin_b2,0);  //
  output_bit(pin_b1,0);
  delay_us(17950);
  affichage();
   }
  break;
  
   case ARRET_DEUX_MOTEURS:
   stop(20);
   break;
  }
}

//terminer
void tourner_d (int1 senss,int16 diff)
{ 
/*
   int puls_hight=0,puls_low=0;
   if (senss==0)
      {
   puls_hight=1100;
   puls_low=18900;
      }
   else if (senss==1)
      {
   puls_hight=1800;
   puls_low=18200;
      }  */
   while(abs(b4) < diff )
  {
      output_bit(pin_b1,1);
      delay_us(1200);
      output_bit(pin_b1,0);
      delay_us(18800);
      affichage();
   }
   stop(20);
} 

//terminer
void tourner_g (int senss,int16 diff)
{  
  /*int puls_hight=0,puls_low=0;
  if (senss==1)
      {
   puls_hight=1100;
   puls_low=18900;
      }
   else if (senss==0)
      {
   puls_hight=1800;
   puls_low=18200;
      } */ 
   while(abs(b5) < diff )
  {
      output_bit(pin_b2,1);
      delay_us(1800);
      output_bit(pin_b2,0);
      delay_us(18200);
      affichage();
   }
   stop(20);
}

//terminer
void avancer_2(int16 diff1,diff2)
{
   while ((abs(b4) < diff1) || (abs(b5) < diff2)) 

   {
      output_bit(pin_b2,1);  //
      output_bit(pin_b1,1);  //
      delay_us(1200);   ////1200
      output_bit(pin_b1,1);  // pour que les deux servo-moteurs tournent a la même vitesse (reglage d'erreur)
      output_bit(pin_b2,0);  //
      delay_us(500);  ///700 
      output_bit(pin_b2,0);  //
      output_bit(pin_b1,0);
      delay_us(18300);
      affichage();
      
   }
   stop(20);
/*      
printf("avancer  D : %ld \t\t G: %d \r\n",diff1,diff2);
      while (abs(b4) < diff1)
      {
      tourner_d(1,diff1);
      }
printf("avancer  D : %ld \t\t G: %d \r\n",diff1, diff2);
      while (abs(b5) < diff2)
      {
      tourner_g(0,diff2);
      }
*/
     

}

void reculer_2(int16 diff1,diff2)
{
while (abs(b5) < diff1 || abs(b4) < diff2) 
   {
      output_bit(pin_b2,1);  //
      output_bit(pin_b1,1);  //
      delay_us(1098);   ////1200
      output_bit(pin_b1,0);  // pour que les deux servo-moteurs tournent a la même vitesse (reglage d'erreur)
      output_bit(pin_b2,1);  //
      delay_us(952);  ///700 
      output_bit(pin_b2,0);  //
      output_bit(pin_b1,0);
      delay_us(17950);
      affichage();
      
   }
   stop(20);
}  




void repeat()
{
int16 diff=0,diff1=0,diff2=0;
int serv=0;
//int1 s1=0,s2=0;
if (input(repeter)==1)
  {
      printf("!!!! Reading !!!! \r\n ");
      delay_ms(2000);  // le temps pour mettre la manette sur sa place
      phase = READING;        //pour lire une seule fois
      i=1;
      v=1;
      b4=0;
      b5 = 0;
      derniere_valeur_M1=0;
      derniere_valeur_M2=0;
      addr_enr_2=(unsigned int8)(read_eeprom(0x00));
      printf(" addr_enr %d \r\n ",addr_enr_2);
      do
      {
         serv=(signed int8)(read_eeprom(v++));
         if ((serv==M1) || (serv==M2))
         {
         valeur = (signed int16)(read_eeprom(v++) + ((int32)(read_eeprom(v++))<<8));
         printf(" valeur : %ld \t\t \r\n",valeur);
         }
         else if (serv == M_12)
         {
         valeurD = (signed int16)(read_eeprom(v++) + ((int32)(read_eeprom(v++))<<8));
         valeurG = (signed int16)(read_eeprom(v++) + ((int32)(read_eeprom(v++))<<8));
         printf(" D : %ld \t\t G: %ld \r\n",valeurD, valeurG);
         }
         
         diff1=abs(valeurD-derniere_valeur_M1);
         diff2=abs(valeurG-derniere_valeur_M2);
         printf(" D : %ld \t\t G: %ld \r\n",diff1, diff2);
         /*
         if (derniere_valeur_M1>valeur)
         {  
            s1=0;
         }
         else
         {
            s1=1;
         }
         if (derniere_valeur_M2>valeur)
         {  
            s2=0;
         }
         else
         {
            s2=1;
         }
         */
         if ((serv==M1) || (serv==M2))
         {
         if (serv==M1)
         {
            diff=abs(valeur-derniere_valeur_M1);
            tourner_d(1,diff);
         }
          else if (serv==M2) 
          {
            diff=abs((valeur-derniere_valeur_M2));
            tourner_g(1,diff);
          }
         
         }
         if (serv==M_12)
         {
         if ((derniere_valeur_M1>valeurD)&&(derniere_valeur_M2>valeurG))
          {
            printf("reculer  D : %ld \t\t G: %ld \r\n",diff1, diff2);
            reculer_2(diff1,diff2);
          }
         else if ((derniere_valeur_M1<valeurD)&&(derniere_valeur_M2<valeurG))
          {
            printf("avancer  D : %ld \t\t G: %ld \r\n",diff1, diff2);
            avancer_2(diff1,diff2);
          }
         }
      printf ("vous etes dans la position %d \r\n",i);
      i++;
      maj_drnr_val();
       delay_ms(1000);
     }while (v<addr_enr_2);
       
     
   }  
}


//terminer
void write()
{

   if(phase != WRITING ) 
   {
      printf("!!!! Write !!!! \r\n ");
      phase = WRITING;
   }
   
   //avancer les deux servos
   if(input(av_serv12)==1 && !last_av_serv12)
   {
      val |= AVANCE_DEUX_MOTEURS ;
      last_av_serv12 = 1;
      //enregistrer
      choix_mot_eeprom=M_12;
   }
   else if (!input(av_serv12) && last_av_serv12)
   {  
      val &= !AVANCE_DEUX_MOTEURS ;
      last_av_serv12 = 0;
   }
   
   
    //reculer les deux servos
        if(input(ar_serv12)==1 && !last_ar_serv12)
   {
      val |= RECULE_DEUX_MOTEURS ;
      last_ar_serv12 = 1;
      //enregistrer
      choix_mot_eeprom=M_12;
   }
   else if (!input(ar_serv12) && last_ar_serv12)
   {  
      val &= !RECULE_DEUX_MOTEURS ;
      last_ar_serv12 = 0;
   }
   
   //avancer le servo droit
       if(input(av_serv1)==1 && !last_c3)
   {
      val |= MOTEUR_AVANT_1 ;
      last_c3 = 1;
      //enregistrer
      choix_mot_eeprom=M1;
   }
   else if (!input(av_serv1) && last_c3)
   {  
      val &= !MOTEUR_AVANT_1 ;
      last_c3 = 0;
   }

   // avancer le servo gauche
   if(input(av_serv2)==1 && !last_a1)
   {
      val |= MOTEUR_AVANT_2 ;
      last_a1 = 1;
      //enregistrer
      choix_mot_eeprom=M2;
      
   }
   else if (!input(av_serv2) && last_a1)
   {   
      val &= !MOTEUR_AVANT_2 ;
      last_a1 = 0;
      //enregistrer    
   }
   
   //Commander les moteurs selon la variable VAL
   dour(val);
}

//terminer

void main()
{

 
 /* for(a=0;a<=255;a+=getenv("FLASH_ERASE_SIZE"))
   {
   erase_program_memory(a);
   }
*/
  enable_interrupts(INT_RB);
  enable_interrupts(global);
  
   printf("!!!! New Start !!!! \r\n ");
   addr_enr=1;
   b4=0;
   b5=0;
   nbre=0;
  while (true)
 {
 while ((input(repeter)==0)&&(input(ENREGISTRE)==0))
 {
  write(); 
 } 
    maj_drnr_val();
    
    enregistrer() ;
    
    repeat();
    
    phase=6;
    stop(20);
  }
}
