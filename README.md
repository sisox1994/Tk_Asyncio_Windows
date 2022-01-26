# Python 視窗化程式範本

本範例使用 Asyncio模組建立背景任務,讓IO處理時不會導致Windows視窗卡死

```python
import asyncio
import threading
import time
import tkinter as tk


global Background_Exit
Background_Exit = False

#設定讓Background Task結束
def set_Background_Exit():
    global Background_Exit
    Background_Exit = True


#確認主程式離開
def Is_Background_Exit():
    global Background_Exit
    if (Background_Exit == True):
        return True

#=======  背景處理區塊  ===================
def do_Somethong_Background():

    #======= do somethong here ==========

    #====================================
    return 0


def Background_Task():

    print('Background Task thread id:', threading.get_ident())
    bg_cnt = 0

    while True:

        #Call 背景處理
        do_Somethong_Background()

        #-----確認Background Task還活著用-----
        time.sleep(0.1)
        if(bg_cnt % 20 == 0):
           print("Background:", bg_cnt/20 )
        bg_cnt += 1     
        #--------------------------------------                    
                
        if (Is_Background_Exit() == True):
            break


#將任務分配到新的Thread
async def do_async_job(func):
    ret = await asyncio.to_thread(func)
    print('job done!')
    set_Background_Exit()
    return ret


#=====  UI處理  ==========
def creat_window():
    print('Windows Task thread id:', threading.get_ident())
    global win
    win = tk.Tk()
    win.title('Main Title')
    win.geometry("250x250")
    win.mainloop()


async def main():

    task1 = asyncio.create_task(do_async_job(Background_Task)) 
    task2 = asyncio.create_task(do_async_job(creat_window))  

    ret_Values = await asyncio.gather(task1, task2)

    for ret in ret_Values:
        print("result=",ret)


if __name__ ==  "__main__":
    asyncio.run(main())
```

