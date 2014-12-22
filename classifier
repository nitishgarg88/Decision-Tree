from copy import deepcopy
import sys
import math
YNODE  = sys.maxint -1
NNODE = sys.maxint -2
MAXLENGTH  =15

class node:
    def __init__(self, data):
        self.data = data
        self.child = []
        
class Classifier:
    def __init__(self):
        self.learn('train.data');
        return;
    def entropy_helper(self, republic, democratic):
        if republic!= 0 and democratic != 0:
            total= republic+democratic
            entropy=(democratic/(total))*math.log((democratic/(total)),2)
            entropy+=(republic/(total))*math.log((republic/(total)),2)
            entropy*=-1 
        else:
            entropy=0  
        temp=[democratic,republic,entropy]
        return temp
    
    def entropy(self,result,instances=None,attribute=None,value=None):
        democratic=0.0
        republic=0.0    
        for i in range(len(result)):
            if attribute == None or instances[i][attribute]==value:
                if result[i]=='democrat':
                    democratic+=1
                else:
                    republic+=1
        return self.entropy_helper(republic,democratic)
    
       
    def getBestAttribute(self,result,instances,attribute_list):
        max=0
        bestAttribute=attribute_list[0]
        for currentAttribute in attribute_list:
            currentGain=0.0
            currentEntropy=self.entropy(result)
            sumOfCurrEn = currentEntropy[0] + currentEntropy[1]
            yEnt=self.entropy(result,instances,currentAttribute,'y')
            yTotalEnt = yEnt[0] + yEnt[1]
            nEnt=self.entropy(result,instances,currentAttribute,'n')
            nTotalEnt = nEnt[0] + nEnt[1]
            qEnt=self.entropy(result,instances,currentAttribute,'?')
            qTotalEnt = qEnt[0] + qEnt[1]
            currentGain=currentEntropy[2]-((yTotalEnt/sumOfCurrEn)*yEnt[2])
            currentGain -= (((nTotalEnt)/(sumOfCurrEn))*nEnt[2])
            currentGain -= ((qTotalEnt/sumOfCurrEn)*qEnt[2]) 
            currentVariableEntropy=  currentGain,currentEntropy[0],currentEntropy[1]
            if currentVariableEntropy>max:
                max=currentVariableEntropy
                bestAttribute=currentAttribute
                
        return bestAttribute
    
    def create_decision_tree(self,result,instances,attribute_list,n=None):    
        if n==None:
            #print attribute_list
            best= self.getBestAttribute(result,instances,attribute_list)
            n=node(best)
            #print best
            ySubTreeLabels=[];nSubTreeLabels=[];qSubTreeLabels=[]
            yInstances=[];nInstances=[];qInstances=[]
            yDemocrats=nDemocrats=qDemocrats=yrepublicans=nrepublicans=qrepublicans=0
            leftattributes=[];
            leftattributes=deepcopy(attribute_list)
            leftattributes.remove(best)
            for i in range(len(result)):
                if(instances[i][best]=='y'):
                    ySubTreeLabels.append(result[i])
                    yInstances.append(instances[i])
                    if result[i]=='democrat':
                        yDemocrats=yDemocrats+1
                    else:
                        yrepublicans=yrepublicans+1
                if(instances[i][best]=='n'):
                    nSubTreeLabels.append(result[i])
                    nInstances.append(instances[i])
                    if result[i]=='democrat':
                        nDemocrats=nDemocrats+1
                    else:
                        nrepublicans=nrepublicans+1
                if(instances[i][best]=='?'):
                    qSubTreeLabels.append(result[i])
                    qInstances.append(instances[i])
                    if result[i]=='democrat':
                        qDemocrats=qDemocrats+1
                    else:
                        qrepublicans=qrepublicans+1
            if yDemocrats>0 and yrepublicans>0:
                n.child.append(self.create_decision_tree(ySubTreeLabels,yInstances,leftattributes))
            elif yDemocrats:
                n.child.append(node(YNODE))
            elif yrepublicans:
                n.child.append(node(NNODE))
            elif (yDemocrats+nDemocrats+qDemocrats)>(yrepublicans+nrepublicans+qrepublicans):
                n.child.append(node(YNODE))
            else:
                n.child.append(node(NNODE))
                
            if nDemocrats>0 and nrepublicans>0:
                n.child.append(self.create_decision_tree(nSubTreeLabels,nInstances,leftattributes))
            elif nDemocrats:
                n.child.append(node(YNODE))
            elif nrepublicans:
                n.child.append(node(NNODE))
            elif (yDemocrats+nDemocrats+qDemocrats)>(yrepublicans+nrepublicans+qrepublicans):
                n.child.append(node(YNODE))
            else:
                n.child.append(node(NNODE))
            if qDemocrats>0 and qrepublicans>0:
                n.child.append(self.create_decision_tree(qSubTreeLabels,qInstances,leftattributes))
            elif qDemocrats:
                n.child.append(node(YNODE))
            elif qrepublicans:
                n.child.append(node(NNODE))
            elif (yDemocrats+nDemocrats+qDemocrats)>(yrepublicans+nrepublicans+qrepublicans):
                n.child.append(node(YNODE))
            else:
                n.child.append(node(NNODE))
    
            return n
        
        return;
    
    def solve_helper(self, query):
        p = self.tree
        item = query[2*self.tree.data]
        flag = 0
        if item=='y' and len(p.child):
            flag = 0
        elif item=='n' and len(p.child)>1:
            flag = 1
        else:
            flag = 2
        item = p.child[flag]    
        while item.data<=MAXLENGTH:
            p = item
            item = query[2*item.data]
            length = len(p.child)
            flag = 0
            if item=='y' and len(p.child):
                flag = 0
            elif item=='n' and len(p.child)>1:
                flag = 1
            else:
                flag = 2
            item = p.child[flag]
        if item.data == YNODE:
            return True;
        return False
    
    
    # Add your code here.
    # Read training data and build your decision tree
    # Store the decision tree in this class
    # This function runs only once when initializing
    # Please read and only read train_data: 'train.data'
    def learn(self, inputFile):
        attributes = [] 
        result=[]
        attrList=[]
        inputFile = open(inputFile, 'r')
        lines = inputFile.readlines()
        for line in lines:
            result.append(line.split()[0]);
            attributes.append(line.split()[1].strip().split(','))
        for i in range(len(attributes[0])):
            attrList.append(i)
        self.tree=self.create_decision_tree(result,attributes,attrList)

    # Add your code here.
    # Use the learned decision tree to predict
    # query example: 'n,y,n,y,y,y,n,n,n,y,?,y,y,y,n,y'
    # return 'republican' or 'republican'
    def solve(self, query):
        if (self.solve_helper(query)):
            return 'democrat'
        return 'republican'
       
