

#include <graphics.h>

#include <conio.h>

#include <ctime>

#include <cmath>

#include <cstdlib>


//窗口大小
struct Point {
	double x, y;
	COLORREF color;
};
const int MAX_POINTS = 256;
const COLORREF colors[MAX_POINTS] = { RGB(255, 192, 203), 

RGB(255, 182, 193), 

RGB(255, 105, 180), 

RGB(255, 20, 147), 

RGB(219, 112, 147), 

RGB(255, 174, 185),

RGB(255, 0, 144) };
const int xScreen = GetSystemMetrics(SM_CXSCREEN);
const int yScreen = GetSystemMetrics(SM_CYSCREEN) - 100;

const double pi = 3.14159265359;
const double e = 2.71828;
const double average_distance = 0.162;
const int quantity = 506;
const int circles = 210;
const int frames = 20; 
const int COLOR_RANGE = 6;
Point origin_points[quantity];
Point points[circles*quantity];
IMAGE images[frames];


int createrandom(int min, int max) {
	return rand() % (max - min + 1) + min;
	
}
void create_data()
{
	int index = 0;
	double x1 = 0, y1 = 0, x2 = 0, y2 = 0;
	for (double radin = 0.1; radin <= 2 * pi; radin += 0.005)
	{
		x2 = 16 * pow(sin(radin), 3);
		y2 = 13 * cos(radin) - 5 * cos(2 * radin) - 2 * cos(3 * radin) - cos(4 * radin);
		
		double distance = sqrt(pow(x2 - x1,2)+pow(y2-y1, 2));
		if (distance > average_distance) {
			x1 = x2;
			y1 = y2;
			origin_points[index].x = x2;
			origin_points[index++].y = y2;

		}
	}
	index = 0;
	for (double size = 0.1, lightness = 1.5; size <= 20; size += 0.1) {
		double p = 1 / (1 + pow(e, 8 - size / 2));
		if (lightness > 1) lightness -= 0.0025;
		for (int i = 0; i < quantity; ++i) {
			if (p > createrandom(0, 100) / 100.0) {
				COLORREF color = colors[createrandom(0, COLOR_RANGE)];
				points[index].color = RGB(GetRValue(color) / lightness, GetGValue(color) / lightness, GetBValue(color) / lightness);
				points[index].x = size * origin_points[i].x + createrandom(-4, 4);
				points[index++].y = size * origin_points[i].y + createrandom(-4, 4);
			}
		}
	}
		int points_size = index;

		for (int f = 0; f < frames; ++f) {
			images[f] = IMAGE(xScreen, yScreen);
			SetWorkingImage(&images[f]);
			setorigin(xScreen / 2, yScreen / 2);

			setaspectratio(1, -1);

			for (index = 0; index < points_size; ++index) {
				double x = points[index].x, y = points[index].y;
				double dis = sqrt(pow(x, 2) + pow(y, 2));
				double dis_in = -0.0009 * dis * dis + 0.35714 * dis + 5;
			
				double x_in = dis_in * x / dis / frames;
				double y_in = dis_in * y / dis / frames;

				points[index].x += x_in;
				points[index].y += y_in;
			
				setfillcolor(points[index].color);
				solidcircle(points[index].x, points[index].y, 1);
			}
		
			for (double size = 17; size < 23; size += 0.3) {

				for (index = 0; index < quantity; ++index) {

					if ((createrandom(0, 100) / 100.0 > 0.6 && size >= 20) || (size < 20 && createrandom(0, 100) / 100.0 > 0.95)) {

						double x, y;

						if (size >= 20) {

							x = origin_points[index].x * size + createrandom(-f * f / 5 - 15, f * f / 5 + 15);

							y = origin_points[index].y * size + createrandom(-f * f / 5 - 15, f * f / 5 + 15);

						}

						else {

							x = origin_points[index].x * size + createrandom(-5, 5);

							y = origin_points[index].y * size + createrandom(-5, 5);

						}

						setfillcolor(colors[createrandom(0, COLOR_RANGE)]);

						solidcircle(x, y, 1);

					}

				}

			}
			setaspectratio(1, 1);

			settextstyle(100, 0, _T("宋体"));

			settextcolor(RGB(255, 182, 193));



			saveimage(_T("爱心.png"), &images[f]);

			setorigin(0, 0);

			setaspectratio(1, 1);

			loadimage(&images[f], _T("爱心.png"));

			

		}

		SetWorkingImage();

}

void init_graphics() {

	HWND hwnd = initgraph(xScreen, yScreen);

	RECT rect;

	SystemParametersInfo(SPI_GETWORKAREA, 0, &rect, 0);

	ShowWindow(hwnd, SW_MAXIMIZE);

	SetWindowPos(hwnd, HWND_TOP, rect.left, rect.top, rect.right - rect.left, rect.bottom - rect.top, SWP_SHOWWINDOW);

	BeginBatchDraw();

	setorigin(xScreen / 2, yScreen / 2);

	setaspectratio(1, -1);

	srand(static_cast<unsigned>(time(0)));

}

	int main() {

		init_graphics();

	//创建窗口
	
	//双缓冲绘图
	
	//爱心粒子创建
	create_data();
	graphdefaults();
	SetWorkingImage();
	int f = 0;
	bool extend = true, shrink = false;
	//主循环
	for (int f = 0; !_kbhit();) {
		//渲染爱心粒子
		putimage(0, 0, &images[f]);
		//刷新窗口
		FlushBatchDraw();

		Sleep(20);
		
		cleardevice();

		if (extend)
			f == 19 ? (shrink = true, extend = false) : ++f;
		else {
			f == 0? (shrink = false, extend = true) : --f;

		}

	}
	//关闭双缓冲
	EndBatchDraw();

	//关闭窗口
	closegraph();
	return 0;
}
