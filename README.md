# A22_K1635
#include<stdio.h>
#include<conio.h>
#include<stdlib.h>

int n=0;
int process[10][7];
int readyQueue[100],burst[10],arrivalTime[10];
int front=0,rear=-1,max=-1,cpuTime=0;
void getData()
{
	FILE *fp;
	char ch;
	int count=0,count1=0;
	fp=fopen("data.txt","r");
	ch=fgetc(fp);
	while(ch!=EOF)
	{
		if(ch!=' ' && n<6)
		{
			n++;
			process[count][1]=ch-'0';
			arrivalTime[count]=ch-'0';
			if(max<arrivalTime[count])
				max=arrivalTime[count];
		}
		else if(ch!=' ')
		{
			char ch1;
			int a=ch-'0';
			int b;
			ch1=fgetc(fp);
			while(ch1!=EOF && ch1!=' ')
			{
				b=ch1-'0';
				a=(a*10)+b;
				ch1=fgetc(fp);
			}
			process[count1][2]=a;
			burst[count1++]=a;
		}
		ch=fgetc(fp);
	}
}
void Enqueue(int a)
{
	rear=rear+1;
	readyQueue[rear]=a;
}
int Dequeue()
{
	int a=readyQueue[front];
	process[a-1][3]=cpuTime+1;
	front=front+1;
	return a;
}
int IsAllExecuted(){
	// checking whether all the process has completed their execution or not......
	
	int i;
	for(i=0;i<n;i++)
	{
		if(process[i][2]>0)
			break;
	}
	
	
	if(i<n)
	return 1;
	return 0;
}

int sortReadyQueue(){
	// Sorting all the processes including the present running process.......
	int temp[n],count=0,i,j;
	for(i=front;i!=rear+1;i++){
	//	printf("\nKK");
			temp[count++]=readyQueue[i];
	}
	// Sorting the process as per priority using bubble sort.......
	
	for(i=count;i>0;i--)
	{
		for(j=0;j<i-1;j++)
		{
			if(process[temp[j+1]-1][2]<process[temp[j]-1][2])
			{
				int k=temp[j+1];
				temp[j+1]=temp[j];
				temp[j]=k;
			}
		}
	}
	j=0;
	if(count!=0)
		for(i=front;i!=rear+1;i++){
			readyQueue[i]=temp[j++];
		}
	else
		return -1;
	return readyQueue[front]-1;
}


void scheduler()
{
	int id,i,count=0,flag1=0;
	while(IsAllExecuted())
	{
		if(cpuTime<=max)
		for(i=0;i<n;i++)
			if(cpuTime==arrivalTime[i])
			{
				Enqueue(i+1);
			}
		id=sortReadyQueue();
		if(id!=-1)
		{
			process[id][2]=process[id][2]-1;
			count++;
			if(process[id][2]<=0)
			{
				Dequeue();
			}
		}
		else if(IsAllExecuted())
		{
			//Idle	
		}
		else
		{
			break;	
		}
		cpuTime++;
	}
}

void calculateWaitingTime()
{
	int i,avg=0;
	printf("\n=======================================================================================================================\n\n");
	printf("Process		WaitingTime\n");
	printf("--------------------------------\n");
	for(i=0;i<n;i++)
	{
		process[i][5]=process[i][4]-burst[i];
		avg+=process[i][5];
		printf("  P%d			%d\n",i+1,process[i][5]);
	}
	avg/=n;
	printf("\nThe Average Waiting Time is %d\n",avg);
}


void calculateTurnAroundTime()
{
	int i,avg=0;
	printf("\n=======================================================================================================================\n\n");
	printf("Process		TurnAroundTime\n");
	printf("------------------------------------\n");
	for(i=0;i<n;i++)
	{
		process[i][4]=process[i][3]-process[i][1];
		avg+=process[i][4];
		printf("  P%d			%d\n",i+1,process[i][4]);
	}
	avg/=n;
	printf("\nThe Average TurnAround Time is %d\n",avg);
	
}

void showDetails()
{
	int i;
	printf("Process		ArrivalTime		BurstTime\n");
	printf("-------------------------------------------\n");
	for(i=0;i<n;i++)
	{
		printf("   %d		   %d		      %d\n",i+1,process[i][1],process[i][2]);
	}
	printf("\n\n");
}
int main()
{
	getData();
	printf("\n\n");
	scheduler();
	printf("KK");
	printf("\n");
	showDetails();
	calculateTurnAroundTime();
	calculateWaitingTime();
	return 0;
}
