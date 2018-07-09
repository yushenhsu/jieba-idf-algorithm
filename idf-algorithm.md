# jieba-idf-algorithm

#IDF權重算法

import nltk
import math
import string
from nltk.corpus import stopwords
from collections import Counter
from nltk.stem.porter import *
from sklearn.feature_extraction.text import TfidfVectorizer
from os import listdir
from os.path import isfile, isdir, join
import pandas as pd
import jieba  
import re
import jieba.analyse

jieba.load_userdict('C:/') 
jieba.analyse.set_stop_words("C:/")

#架構:
'''
爬文後的標題，變成txt檔， 讀檔進corpus
切詞後計算idf後寫入.idf檔
'''


def data_cleansing(content):
    letters_only = re.sub("\W", "", content)   #\W 比對非「數字字母字元或底線字母」，等效於 [^A-Za-z0-9_]
    words = letters_only.lower().split()   
    return( " ".join( words )) 


def n_containing(word, count_list):
    return sum(1 for count in count_list if word in count)

def idf(word, count_list):
    return '%s %f'%(word,math.log(len(count_list) / (1 + n_containing(word, count_list))))
    

# 指定要列出所有檔案的目錄
mypath = "D:/mobile01_file"

# 取得所有檔案與子目錄名稱
files = listdir(mypath)

#讀資料進corpus

corpus = []
for f in files:   
    # 產生檔案的絕對路徑
    fullpath = join(mypath, f)
    if isfile(fullpath):
        print("檔案：", f)
    text= open('D:/mobile01_file/'+f, 'r',encoding='utf-8-sig')   #注意!! 檔案要存成UTF-8格式
    text_article=text.read()
    #print(text_article)
    corpus.append(text_article)
    text.close()

print(corpus)

#先清空idf檔
f = open('C:/','w', encoding='UTF-8')
f.truncate();

#寫入idf權重
len_corpus=len(corpus)
print(len_corpus)
for i in range(0,len_corpus):
    words_line=jieba.cut(data_cleansing(corpus[i]),cut_all=False)
    words_line=list(words_line)
    
    #去stopwords
    x=[]
    for a in words_line:
        if a not in [line.rstrip() for line in open('C:/', encoding='UTF-8')]:
            x.append(a)
    print(x)   
    #print(words_line)    
    len_words_line=len(x)    
    for j in range(0,len_words_line):
        print(idf(x[j],corpus))
        f = open('C:/Users/easonhsu/Desktop/newidf.txt','a', encoding='UTF-8')
        f.write(idf(x[j],corpus)+'\n')  # 寫入idf值    # \n讓寫入換行
        f.close()
        
