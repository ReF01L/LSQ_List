#include <malloc.h>
 
#include "linear_sequence.h"
 
typedef struct sNode
{
    struct sNode *prev;
    struct sNode *next;
    LSQ_BaseTypeT data;
}sNode;

typedef struct
{
    sNode *head;
    sNode *tail;
    LSQ_IntegerIndexT count;
}sContainer;
 
typedef struct
{
    sContainer *cont;
    sNode *node;
}sIterator;
 
static sIterator *createIteration(LSQ_HandleT handle);
static sNode *findElement(LSQ_HandleT handle, LSQ_IntegerIndexT index);
 
LSQ_HandleT LSQ_CreateSequence()
{
    sContainer *cont = (sContainer*)malloc(sizeof(sContainer));
    if (cont == LSQ_HandleInvalid)
        return LSQ_HandleInvalid;
 
    cont->count = 0;
    cont->head = (sNode*)malloc(sizeof(sNode));
    cont->tail = (sNode*)malloc(sizeof(sNode));
 
    cont->head->prev = cont->head;
    cont->head->next = cont->tail;
 
    cont->tail->prev = cont->head;
    cont->tail->next = cont->tail;
 
    return cont;
}

void LSQ_DestroySequence(LSQ_HandleT handle)
{
    sContainer* cont = (sContainer*)handle;
    
    if (cont == LSQ_HandleInvalid)
        return;
    
    while (cont->head != cont->tail)
    {
        sNode *tmp = cont->head;
        cont->head = cont->head->next;
        free(tmp);
    }
    
    free(cont->tail);
    free(cont);
}
 
LSQ_IntegerIndexT LSQ_GetSize(LSQ_HandleT handle)
{
    if (handle == NULL)
        return 0;
 
    return ((sContainer*)handle)->count;
}
 
int LSQ_IsIteratorDereferencable(LSQ_IteratorT iterator)
{
    if (iterator == NULL)
        return 0;
 
    sIterator *iter = (sIterator*)iterator;
 
    return iter->node != iter->cont->head && iter->node != iter->cont->tail;
}
 
int LSQ_IsIteratorPastRear(LSQ_IteratorT iterator)
{
    if (iterator == NULL)
        return 0;
 
    return ((sIterator*)iterator)->node == ((sIterator*)iterator)->cont->tail;
}
 
int LSQ_IsIteratorBeforeFirst(LSQ_IteratorT iterator)
{
    if (iterator == NULL)
        return 0;
 
    return ((sIterator*)iterator)->node == ((sIterator*)iterator)->cont->head;
}
 
LSQ_BaseTypeT* LSQ_DereferenceIterator(LSQ_IteratorT iterator)
{
    return iterator == NULL ? NULL : &(((sIterator*)iterator)->node->data);
}
 
LSQ_IteratorT LSQ_GetElementByIndex(LSQ_HandleT handle, LSQ_IntegerIndexT index)
{
    sIterator *iter = createIteration(handle);
 
    if (iter == NULL)
        return NULL;
 
    iter->node = findElement(handle, index);
 
    return iter;
}
 
LSQ_IteratorT LSQ_GetFrontElement(LSQ_HandleT handle)
{
    sIterator *iter = createIteration(handle);
 
    if (iter == NULL)
        return NULL;
 
    iter->node = findElement(handle, 0);
 
    return iter;
}
 
LSQ_IteratorT LSQ_GetPastRearElement(LSQ_HandleT handle)
{
    sIterator *iter = createIteration(handle);
 
    if (iter == NULL)
        return NULL;
 
    iter->node = findElement(handle, (((sContainer*)handle)->count));
 
    return iter;
}
 
void LSQ_DestroyIterator(LSQ_IteratorT iterator)
{
    free(iterator);
}
 
void LSQ_AdvanceOneElement(LSQ_IteratorT iterator)
{
    LSQ_ShiftPosition(iterator, 1);
}
 
void LSQ_RewindOneElement(LSQ_IteratorT iterator)
{
    LSQ_ShiftPosition(iterator, -1);
}
 
void LSQ_ShiftPosition(LSQ_IteratorT iterator, LSQ_IntegerIndexT shift)
{
    if (iterator == NULL)
        return;
 
    LSQ_IntegerIndexT i;
 
    for (i = 0; i < abs(shift); i++)
    {
        if (shift > 0)
            ((sIterator*)iterator)->node = ((sIterator*)iterator)->node->next;
        else
            ((sIterator*)iterator)->node = ((sIterator*)iterator)->node->prev;
    }
}
 
void LSQ_SetPosition(LSQ_IteratorT iterator, LSQ_IntegerIndexT pos)
{
    if (iterator == NULL)
        return;
 
    ((sIterator*)iterator)->node = findElement(((sIterator*)iterator)->cont, pos);
}
 
void LSQ_InsertFrontElement(LSQ_HandleT handle, LSQ_BaseTypeT element)
{
    LSQ_IteratorT iterator = LSQ_GetFrontElement(handle);
    LSQ_InsertElementBeforeGiven(iterator, element);
    LSQ_DestroyIterator(iterator);
}
 
void LSQ_InsertRearElement(LSQ_HandleT handle, LSQ_BaseTypeT element)
{
    LSQ_IteratorT iterator = LSQ_GetPastRearElement(handle);
    LSQ_InsertElementBeforeGiven(iterator, element);
    LSQ_DestroyIterator(iterator);
}
 
void LSQ_InsertElementBeforeGiven(LSQ_IteratorT iterator, LSQ_BaseTypeT newElement)
{
    if (iterator == NULL || LSQ_IsIteratorBeforeFirst(iterator))
        return;
 
    sNode *tmp = (sNode*)malloc(sizeof(sNode));
 
    if (tmp == NULL)
        return;
 
    tmp->prev = ((sIterator*)iterator)->node->prev;
    tmp->next = ((sIterator*)iterator)->node;
    tmp->data = newElement;
 
    ((sIterator*)iterator)->cont->count++;
    ((sIterator*)iterator)->node = tmp;
}
 
void LSQ_DeleteFrontElement(LSQ_HandleT handle)
{
    LSQ_IteratorT iterator = LSQ_GetFrontElement(handle);
    LSQ_DeleteGivenElement(iterator);
    LSQ_DestroyIterator(iterator);
}
 
void LSQ_DeleteRearElement(LSQ_HandleT handle)
{
    LSQ_IteratorT iterator = LSQ_GetPastRearElement(handle);
    LSQ_RewindOneElement(iterator);
    LSQ_DeleteGivenElement(iterator);
    LSQ_DestroyIterator(iterator);
}
 
void LSQ_DeleteGivenElement(LSQ_IteratorT iterator)
{
    if (iterator == NULL)
        return;
 
    if (!LSQ_IsIteratorDereferencable(iterator))
        return;
 
    sNode *tmp = ((sIterator*)iterator)->node->next;
 
    ((sIterator*)iterator)->node->prev->next = tmp;
    ((sIterator*)iterator)->node->next->prev = ((sIterator*)iterator)->node->prev;
 
    free(((sIterator*)iterator)->node);
 
    ((sIterator*)iterator)->cont->count--;
    ((sIterator*)iterator)->node = tmp;
}
 
static sIterator *createIteration(LSQ_HandleT handle)
{
    sIterator *iter = (sIterator*)malloc(sizeof(sIterator));
 
    if (iter == NULL || handle == LSQ_HandleInvalid)
        return NULL;
 
    iter->cont = (sContainer*)handle;
 
    return iter;
}
 
static sNode *findElement(LSQ_HandleT handle, LSQ_IntegerIndexT index)
{
    sContainer *cont = (sContainer*)handle;
    LSQ_IntegerIndexT i;
    sNode *tmp;
 
    if (index > cont->count / 2)
        for (i = cont->count, tmp = cont->tail; i > index; tmp = tmp->prev, i--);
 
    else
        for (i = -1, tmp = cont->head; i < index; tmp = tmp->next, i++);
 
    return tmp;
}
