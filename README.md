#include<stdio.h>
#include<stdlib.h>  //system,rand,sand
#include<conio.h>   //kbhit
#include<windows.h> //sleep
#include<time.h>    //time

#define H 16
#define L 16
#define up      0
#define down    1
#define left    2
#define right   3
#define holdon  4

int length ;        //蛇长
int food;           //食物位置

char map[H][L];
int snake[H * L];   //包括横竖坐标

void _initialize( void );      //初始化
void _map( void );             //显示函数
void _snake( char direction ); //蛇的坐标
char _key( void );             //按键
int _death( void );            //是否扑街
void _food( void );            //产生食物
void _eat( void );             //是否吃到
int _speed( void );            //难度

int main()
{
    int speed = _speed();
    _initialize();
    int i;
    getch();
    char direction = right, dir;
    while( 1 )
    {
        dir = _key();
        if(dir != holdon)
            direction = dir;
        _snake( direction );
        if( _death() )
        {
            printf("\t\tGAME OVER!\n\t\t总得分：%d\n",((length - 4) * (6 - speed / 100)));
            break;
        }
        if(length == H * L)
        {
            printf("\t\t大神！请收下小弟膝盖orz\n");
            break;
        }
        _eat();
        _map( );
        Sleep( speed );
    }
    getch();
}
void _initialize( void )
{
    /*
    **  地图初始化为“ ”
    */
    system("title SNAKE");
    int i, j, cx;
    for(i = 0; i < H; i++)
        for(j = 0; j < L; j++)
            map[i][j] = ' ';
    /*
    **  蛇坐标,长度初始化
    */
    length = 4;
    snake[0] = H / 2 * 100 + L / 2 - 2;
    snake[1] = snake[0] - 1;
    snake[2] = snake[1] - 1;
    snake[3] = snake[2] - 1;
    _food();
    _map();
    printf("\t\tw上s下a左d右\n");
    printf("\t\t按任意键开始游戏\n");
}
void _map( void )
{
    int i, j;
    int cx;
    for(cx = 1; cx < length; cx++)
        map[snake[cx] / 100][snake[cx] % 100] = 'o';    //蛇身为“o”
    map[snake[length] / 100][snake[length] % 100] = ' ';//清除蛇走过的路
    map[snake[0] / 100][snake[0] % 100] = 2;            //蛇头
    map[food / 100][food % 100] = 3;                    //食物
    system("cls");
    /*
    **  建立墙体，计分器
    */
    for(i = 0; i <= 3 * L + 1; i++)
        printf("%c",'=');
    printf("\t\t计数：%d\n",(length - 4));
    for(i = 0; i < H; i++)
    {
        printf("%c",'^');
        for(j = 0; j < L; j++)
            printf(" %c ",map[i][j]);
        printf("%c\n",'^');
    }
    for(i = 0; i <= 3 * L + 1; i++)
        printf("%c",'=');
    printf("\n");
}
void _snake( char direction )
{
    int i;
    for( i = length; i > 0; i--)
    {
        snake[i] = snake[i - 1];    //  蛇的蠕动
    }
    /*
    **  蛇头
    */
    switch( direction )
    {
        case up     : snake[0] -= 100;  break;
        case down   : snake[0] += 100;  break;
        case left   : snake[0] -= 1;    break;
        case right  : snake[0] += 1;    break;
    }
}
char _key( void )
{
    char direction;
    if( kbhit() )
        direction = getch();
    switch ( direction )
    {
        case 'w' : return up; break;
        case 's' : return down;break;
        case 'a' : return left;break;
        case 'd' : return right;break;
        default  : return holdon;
    }
}
int _death( void )
{
    /*
    **  撞墙与撞尾
    */
    if((snake[0]/100>=H)|(snake[0]/100<0)|(snake[0]%100>=L)|(snake[0]%100<0))
        return 1;
    int i;
    for(i = 1; i < length; i++)
        if(snake[i] == snake[0])
            return 1;
    return 0;

}
void _food( void )
{
    srand( (unsigned) time(0) );
    int i, test = 1;
    /*
    **  产生食物，但得避开蛇体
    */
    while( test == 1 )
    {
        test = 0;
        food = ( rand() % H ) * 100 + ( rand() % L );
        for( i = 0; i < length; i++)
        {
            if( food == snake[i] )
                test = 1;
        }
    }
}
void _eat( void )
{
    if( snake[0] == food )
        {
            length += 1;
            _food();
        }
}
int _speed( void )
{
    char choose;
    int difficulty = 1, speed;
    do
    {
        system("cls");
        printf("\n\n\n\t\t贪食蛇\n\n");
        printf("\t\t按空格增加难度\n");
        printf("\t\t按回车键确认\n");
        printf("\t\t难度:\t%d\n",difficulty);
        choose = getch();
        if(( choose == ' ') && (difficulty < 5))
            difficulty += 1;
    }
    while( choose != 13);
    switch( difficulty )
    {
        case 1 : speed = 500;break;
        case 2 : speed = 400;break;
        case 3 : speed = 300;break;
        case 4 : speed = 200;break;
        default: speed = 100;
    }
    return speed;
}
