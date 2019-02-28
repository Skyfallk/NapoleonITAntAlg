# NapoleonITAntAlg
Test task.
import numpy as np
np.random.seed = 7
#функция, обновляющая мартицу феромонов
def TauNew(tau, ants, colony_ways, colony_l, r) :
    for i in range(0,len(m)):
        for j in range(0,len(m)):
            if tau[i,j] != 0:
                tau[i,j] = (1-r)*tau[i,j]
    
    
    for i in range(0, ants) :
        w = colony_ways[i]
        for j in range(len(w)-1) :
            tau[w[j], w[j+1]] = tau[w[j], w[j+1]] + q / colony_l[i] 
        tau[ w[-1], w[0] ]= tau[ w[-1], w[0] ] + q/colony_l[i]
        
    return tau
#инициализируем начальную матрицу
m = np.random.randint(1,50,(20,20))
for i in range(len(m)) :
    for j in range(len(m)) :
        if j==i :
            m[i,j] = 0
#инициализируем начальную матрицу фереомонов
tau = np.random.sample((20,20))

for i in range(len(tau)) :
    for j in range(len(tau)) :
        if j==i :
            tau[i,j] = 0
#инициализируем параметры
a, b, q = 1, 2, 5          
r = 0.8
way = [0]#список с исторей пройденных вершин
city = [i for i in range(0, len(m))]#список всех вершин
vis = list(set(city)-set(way))#список возможных вершин для перехода
p = {}#словарь хранящий номер вершины и вероятность перехода в нее
l = 0
while len(vis)>0 :
    
    for i in vis :
        #заполняем словарь номерами вершин и соответствующим им вероятностям перехода
        s = 0
        for j in vis :
            s = s + ((tau[ way[-1], j])**a) / ((m[ way[-1], j])**b)
            
        p[i] =  ((tau[ way[-1], i])**a / (m[ way[-1], i])**b) / s
         
    #массив с номерами доступных вершин
    n1 = np.array(list(p.keys()))
    #массив с соответствующими вероятностями перехода
    n2 = np.array(list(p.values()))
    #опираясь на вероятности случайно выбираем вершину для перехода и добавляем ее в список путей
    way.append(np.random.choice(n1, p = n2))
    l = l + m[way[-2], way[-1]]
    #изменяем список вершин доступных для перехода
    vis = list(set(city)-set(way))
    p.clear()
#первый путь
best_way = way.copy()
#стоимость первого пути
best_l = l.copy()
#количесвто муравьев
ants = 20
#список для хранения путей муравьев
colony_ways = []
#список стоимостей пройденных путей
colony_l = []
#количесвто эпох
t_max = 100
for t in range(0,t_max) :     
    for k in range(0,ants) :
        start = np.random.choice(len(m))
        way = [start]
        vis = list(set(city)-set(way))
        p = {}
        l = 0

        while len(vis)>0 :

            for i in vis :
                #заполняем словарь номерами вершин и соответствующим им вероятностям перехода
                s = 0
                for j in vis :
                    s = s + ((tau[ way[-1], j])**a) / ((m[ way[-1], j])**b)

                p[i] =  ((tau[ way[-1], i])**a / (m[ way[-1], i])**b) / s

            #массив с номерами доступных вершин
            n1 = np.array(list(p.keys()))
            #массив с соответствующими вероятностями перехода
            n2 = np.array(list(p.values()))
            #опираясь на вероятности случайно выбираем вершину для перехода и добавляем ее в список путей
            way.append(np.random.choice(n1, p = n2))
            l = l + m[way[-2], way[-1]]
            #изменяем список вершин доступных для перехода
            vis = list(set(city)-set(way))
            p.clear()

        l = l + m[ way[-1], way[0] ]

        colony_ways.append(way)
        colony_l.append(l) 

    if best_l > min(colony_l) :
        best_way = colony_ways[colony_l.index(min(colony_l))]
        best_l = min(colony_l)

    tau = TauNew(tau, ants, colony_ways, colony_l, r)
        
print('лучший путь', best_way)
print('стоимость лучшего пути', best_l)
