# alarm
//闹钟可实现闹钟，倒计时，秒表，查看实时时间4个功能
#define _CRT_SECURE_NO_WARNINGS
#include<stdio.h>
#include<graphics.h>
#include<conio.h>
#include<time.h>
#include<windows.h> 
int main()
{
	int mode = 0;
	int ring_flag = 0;
	int alarm_h = 0;
	int alarm_m = 0;
	int countdown_run = 0;       // 运行状态：0=暂停 1=运行
	clock_t cd_start = 0;        // 点击开始时的时钟值
	double cd_remain = 60000;    // 剩余总毫秒数，默认1分钟
	double cd_backup = 60000;    // 备份设置的时长，暂停/重置用
	int set_h = 1;               // 设置的小时数（滚轮调整用）
	int set_m = 0;
	int stopwatch_run = 0;
	clock_t sw_start = 0;
	double sw_total = 0;
	initgraph(400, 300);
	setbkcolor(WHITE);
	MOUSEMSG m = { 0 };//鼠标
	while (1)
	{
		BeginBatchDraw();
		cleardevice();
		time_t t;
		struct tm* now;
		time(&t);
		now = localtime(&t);
		int cur_h = now->tm_hour;
		int cur_m = now->tm_min;
		int cur_sec = now->tm_sec;
		if (stopwatch_run)
		{
			sw_total = (double)(clock() - sw_start) / CLOCKS_PER_SEC * 1000;
		}
		if (countdown_run)
		{
			
			double passed = (double)(clock() - cd_start) / CLOCKS_PER_SEC * 1000;
			cd_remain = cd_backup - passed; 
			if (cd_remain <= 0)
			{
				cd_remain = 0;
				countdown_run = 0;
				Beep(1200, 1500);
			}
		}
		switch (mode)
		{
		case 0:
		{
			setlinecolor(BLACK);
			settextstyle(30, 0, _T("宋体"));
			outtextxy(130, 30, _T("多功能工具箱"));
			settextstyle(20, 0, _T("宋体"));
			rectangle(60, 80, 170, 130); outtextxy(90, 95, _T("闹钟"));
			rectangle(230, 80, 340, 130); outtextxy(255, 95, _T("实时时钟"));
			rectangle(60, 170, 170, 220); outtextxy(90, 185, _T("秒表"));
			rectangle(230, 170, 340, 220); outtextxy(250, 185, _T("倒计时"));
		}
		break;

		case 1:
		{
			setlinecolor(BLACK); settextstyle(40, 0, _T("宋体"));
			settextcolor(BLACK);
			rectangle(100, 100, 180, 160); rectangle(220, 100, 300, 160);
			TCHAR time_strh[3], time_strm[3];
			_stprintf_s(time_strh, 3, _T("%02d"), alarm_h);
			_stprintf_s(time_strm, 3, _T("%02d"), alarm_m);
			int texth_w = textwidth(time_strh), texth_h = textheight(time_strh);
			int textm_w = textwidth(time_strm), textm_h = textheight(time_strm);
			outtextxy(100 + (70 - texth_w) / 2, 100 + (60 - texth_h) / 2, time_strh);
			outtextxy(220 + (70 - textm_w) / 2, 100 + (60 - textm_h) / 2, time_strm);
			outtextxy(195, 110, _T(":"));
			settextstyle(15, 0, _T("宋体"));
			rectangle(20, 250, 80, 280);
			outtextxy(35, 258, _T("返回"));
		}
		break;
		case 2:
		{
			settextstyle(20, 0, _T("宋体")); outtextxy(140, 30, _T("当前时间"));
			TCHAR time_str[20];
			_stprintf_s(time_str, 20, _T("%02d:%02d:%02d"), cur_h, cur_m, cur_sec);
			settextstyle(50, 0, _T("宋体"));
			int w = textwidth(time_str);
			outtextxy((400 - w) / 2, 120, time_str);
			settextstyle(15, 0, _T("宋体"));
			rectangle(20, 250, 80, 280);
			outtextxy(35, 258, _T("返回"));

		}break;
		case 3:
		{
			settextstyle(20, 0, _T("宋体"));
			outtextxy(150, 30, _T("秒表"));
			int min = (int)(sw_total / 1000) / 60;
			int sec = (int)(sw_total / 1000) % 60;
			int ms = (int)sw_total % 1000;
			TCHAR sw_str[20];
			_stprintf_s(sw_str, 20, _T("%02d:%02d:%02d"), min, sec, ms);
			settextstyle(45, 0, _T("宋体"));
			int w = textwidth(sw_str);
			outtextxy((400 - w) / 2, 100, sw_str);
			settextstyle(18, 0, _T("宋体"));
			rectangle(100, 200, 180, 235);
			outtextxy( 115, 207, stopwatch_run ? _T("暂停") : _T("开始"));
			rectangle(220, 200, 300, 235);
			outtextxy(245, 207, _T("重置"));
			settextstyle(15, 0, _T("宋体"));
			rectangle(20, 250, 80, 280);
			outtextxy(35, 258, _T("返回"));
		}break;
		case 4: // 倒计时（新增秒数显示）
		{
			settextstyle(20, 0, _T("宋体"));
			outtextxy(150, 40, _T("倒计时"));

			// 拆分时、分、秒
			int total_sec = (int)(cd_remain / 1000);
			int cd_h = total_sec / 3600;
			int cd_m = (total_sec % 3600) / 60;
			int cd_s = total_sec % 60;

			// 三个框：时、分、秒
			rectangle(60, 100, 130, 160);
			rectangle(165, 100, 235, 160);
			rectangle(270, 100, 340, 160);

			TCHAR str_h[3], str_m[3], str_s[3];
			_stprintf_s(str_h, 3, _T("%02d"), cd_h);
			_stprintf_s(str_m, 3, _T("%02d"), cd_m);
			_stprintf_s(str_s, 3, _T("%02d"), cd_s);

			int wh = textwidth(str_h), hh = textheight(str_h);
			int wm = textwidth(str_m), hm = textheight(str_m);
			int ws = textwidth(str_s), hs = textheight(str_s);

			outtextxy(60 + (70 - wh) / 2, 100 + (60 - hh) / 2, str_h);
			outtextxy(165 + (70 - wm) / 2, 100 + (60 - hm) / 2, str_m);
			outtextxy(270 + (70 - ws) / 2, 100 + (60 - hs) / 2, str_s);

			outtextxy(145, 110, _T(":"));
			outtextxy(250, 110, _T(":"));

			settextstyle(18, 0, _T("宋体"));
			rectangle(100, 200, 180, 235);
			outtextxy(115, 207, countdown_run ? _T("暂停") : _T("开始"));
			rectangle(220, 200, 300, 235);
			outtextxy(245, 207, _T("重置"));

			settextstyle(15, 0, _T("宋体"));
			rectangle(20, 250, 80, 280);
			outtextxy(35, 258, _T("返回"));
		}
		break;
		}
		while (MouseHit())
		{
			m = GetMouseMsg();

			if (m.uMsg == WM_LBUTTONDOWN)
			{
				// 通用返回
				if (mode != 0 && m.x >= 20 && m.x <= 80 && m.y >= 250 && m.y <= 280)
				{
					mode = 0;
				}

				// 主菜单按钮
				if (mode == 0)
				{
					if (m.x >= 60 && m.x <= 170 && m.y >= 80 && m.y <= 130)  mode = 1;
					if (m.x >= 230 && m.x <= 340 && m.y >= 80 && m.y <= 130) mode = 2;
					if (m.x >= 60 && m.x <= 170 && m.y >= 170 && m.y <= 220) mode = 3;
					if (m.x >= 230 && m.x <= 340 && m.y >= 170 && m.y <= 220)mode = 4;
				}

				// 秒表按钮
				if (mode == 3)
				{
					if (m.x >= 100 && m.x <= 180 && m.y >= 200 && m.y <= 235)
					{
						if (!stopwatch_run)
						{
							sw_start = clock() - (clock_t)(sw_total * CLOCKS_PER_SEC / 1000);
							stopwatch_run = 1;
						}
						else
						{
							stopwatch_run = 0;
						}
					}
					if (m.x >= 220 && m.x <= 300 && m.y >= 200 && m.y <= 235)
					{
						stopwatch_run = 0;
						sw_total = 0;
					}
				}

				// 倒计时按钮
				if (mode == 4)
				{
					if (m.x >= 100 && m.x <= 180 && m.y >= 200 && m.y <= 235)
					{
						if (!countdown_run)
						{
							cd_start = clock();
							cd_backup = cd_remain;
							countdown_run = 1;
						}
						else
						{
							cd_backup = cd_remain;
							countdown_run = 0;
						}
					}
					if (m.x >= 220 && m.x <= 300 && m.y >= 200 && m.y <= 235)
					{
						countdown_run = 0;
						cd_remain = (set_h * 3600 + set_m * 60) * 1000.0;
						cd_backup = cd_remain;
					}
				}
			}

			// 闹钟滚轮调时间
			if (m.uMsg == WM_MOUSEWHEEL && mode == 1)
			{
				if (m.x >= 100 && m.x <= 180 && m.y >= 100 && m.y <= 160)
				{
					if (m.wheel > 0) {
						alarm_h++;
						if (alarm_h > 23) alarm_h = 0;
					}
					else {
						alarm_h--;
						if (alarm_h < 0) alarm_h = 23;
					}
				}
				else if (m.x >= 220 && m.x <= 300 && m.y >= 100 && m.y <= 160)
				{
					if (m.wheel > 0) {
						alarm_m++;
						if (alarm_m > 59) alarm_m = 0;
					}
					else {
						alarm_m--;
						if (alarm_m < 0) alarm_m = 59;
					}
				}
			}

			// 倒计时滚轮调时间（暂停时生效，支持时、分、秒全部可调）
			if (m.uMsg == WM_MOUSEWHEEL && mode == 4)
			{
				if (!countdown_run)
				{
					// 调整小时
					if (m.x >= 60 && m.x <= 130 && m.y >= 100 && m.y <= 160)
					{
						if (m.wheel > 0) {
							set_h++;
							if (set_h > 23) set_h = 0;
						}
						else {
							set_h--;
							if (set_h < 0) set_h = 23;
						}
					}
					// 调整分钟
					else if (m.x >= 165 && m.x <= 235 && m.y >= 100 && m.y <= 160)
					{
						if (m.wheel > 0) {
							set_m++;
							if (set_m > 59) set_m = 0;
						}
						else {
							set_m--;
							if (set_m < 0) set_m = 59;
						}
					}
					// 同步更新剩余时间
					cd_remain = (set_h * 3600 + set_m * 60) * 1000.0;
					cd_backup = cd_remain;
				}
			}
		}
			
			if (cur_h == alarm_h && cur_m == alarm_m)
			{
				if (ring_flag == 0)
				{
					Beep(1000, 10000);
					
					ring_flag = 1;
				}
			}
			else
			{
				ring_flag = 0;
			}
			if (_kbhit())
			{
				int k = _getch();
				if (k == 27) {
					if (mode == 0)break;
					else mode = 0;
				}

			}
		
		FlushBatchDraw();
	}
	closegraph();

	return 0;
}
