---
title: "[자료구조] 해시 테이블 (hash table)"
category: data structure
tag: ["python", "자료구조", "data structure", "hash table", "hash", "해시 테이블", "해시"]
---
# 개요

해시에 대한 내용은 [hashlib](/knowledge/python-hashlib/) 문서 참조.

해시 테이블은 키-값(key-value) 데이터 구조를 띠는 자료구조로, 키를 해시 함수로 해싱하고 나온 해시값을 인덱스로 사용하여 이 인덱스에 value을 매핑함으로써 결과적으로 key와 value를 매핑하는 방식이다. 

해시 테이블 내에서 데이터가 저장되는 각 공간을 해시 버킷 또는 간단히 버킷(bucket)이라고 한다. 슬롯(slot)이라고도 불린다. 

키와 값이 일대일 대응시키게 하려면 해시 충돌의 가능성을 없애거나 따로 해결하는 방법을 모색해야 한다. 그렇지 않으면 해시 테이블 사용에 혼란이 오기 때문이다. 예를 들어 ‘a’라는 키가 있고, 이 키에 대응되는 값을 ‘apple’이라고 하고, 이 키의 인덱스가 1이라고 가정하면, ‘b’라는 키와 그 키의 값이 ‘banana’이고 인덱스도 1일 경우, 해당 버킷에 어떤 값이 저장되어야 할지 혼란이 온다. ‘a’ 키를 저장하고 그 후에 ‘b’키를 저장하면 해당 버킷의 값이 덮어씌워져서 ‘banana’라는 값이 저장될 것이다. 그러면 ‘a’라는 키를 이용해 해당 값을 호출하면 ‘banana’라는 엉뚱한 값이 나올 것이다. 이러면 해시 테이블의 기능을 상실하게 되는 셈이다. 따라서 해시 테이블을 만드려면 해시 충돌의 가능성을 없애거나 해당 문제를 처리하는 다른 방법이 필요하다.

해시 테이블의 조회, 삽입, 삭제의 시간복잡도는 O(1)이다. 그러나 해시 충돌이 생기는 경우 이를 chaining 방법으로 해결하는 경우, 각 버킷에 대한 시간복잡도는 O(n)으로 늘어난다. chaining 개념은 다음에 기술.

# 해시함수

해시 함수에는 여러 종류가 있다. 이 때 중요한 것은 해시 충돌이 최대한 일어나지 않게 해야 하기 때문에, 해시 함수를 구현할 시 해시값들이 특정 값 영역에 치우치지 않도록 고르게 분포하게 하는 게 좋다. 

- division method: 숫자 키를 해시테이블의 크기 (데이터를 최대로 담을 수 있는 버킷 수)로 나눈 나머지를 해시값으로 채택하는 방식. 즉,
해시값 = 숫자 키 % 해시테이블 크기.
해시 충돌을 최대한 방지하기 위해서, 해시테이블의 크기를 소수(prime number)로 정하고, 2의 제곱수와 최대한 먼 값을 사용하는 것이 좋다고 한다. 그러나 이 경우, 원래보다 더 큰 공간을 잡아야할 수도 있어 이 경우 남는 공간이 발생해 메모리 공간 상의 비효율성을 야기한다고도 한다.
다른 해시 함수를 사용하여 나온 해시 값이 숫자일 경우, 이 숫자를 전체 해시테이블의 크기로 나눈 값을 인덱스로 사용하는 방법도 있다.
- digit folding: key가 문자열일 경우, key의 각 문자들을 ASCII (또는 유니코드 값) 코드값으로 바꾸고 이 코드값들의 총합을 해시값으로 사용하는 방법.
- Universal hashing: 서로 다른 해시함수들을 모아두고, 그 중 무작위로 해시함수를 택해 해시값을 만드는 방법. 서로 다른 해시함수들은 같은 값을 입력하더라도 그 해시값이 달라 해시 충돌을 최소화할 수 있다.

이 외에도 Multiplication method 등 여러 해시 함수들이 존재한다. 

# 해시 충돌 해결법

아무리 좋은 해시 함수를 골랐어도 해시 충돌이 여전히 존재할 수 있다. 이렇게 해시 충돌을 해결하는 방법들이 존재한다.

## Chaining

Separate Chaining, 분리 연결법이라고도 한다. 

똑같은 버킷에서 해시 충돌이 일어나면 기존에 해당 버킷에 있던 값과 새로 삽입된 값을 연결리스트(linked list)로 연결한다.

![chaining 기법. 위 사진에서 “John Smith”키와 “Sandra Dee” 키의 해시값이 충돌한다. 이 경우, 두 키의 값을 위 사진처럼 연결리스트로 연결하여 해시 충돌 문제를 해결한다. [4] 참조.](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbTF67c%2FbtqL7xx3OGw%2FDM8KEKU5x7dx6Nks4JR7K1%2Fimg.png)

chaining 기법. 위 사진에서 “John Smith”키와 “Sandra Dee” 키의 해시값이 충돌한다. 이 경우, 두 키의 값을 위 사진처럼 연결리스트로 연결하여 해시 충돌 문제를 해결한다. [4] 참조.

해시 충돌이 생겨 새로운 값을 저장하기 위해 연결 리스트의 노드를 추가하는 경우, 추가 메모리를 사용하여 새 노드를 저장한다. 즉 해시 테이블의 크기는 고정된 채 해시 충돌이 생기는 만큼 메모리를 추가적으로 사용하게 된다. 애초에 해시 충돌을 고려하여 해시 테이블의 크기를 더 크게 잡는 것보다는 해시 충돌이 생기는 데이터에 대해서만 chaining 기법으로 해결하면 되어서 처음에 해시 테이블의 크기를 크게 잡을 필요가 없어진다. (메모리를 동적으로 사용하게 된다)

이렇게 구성하면 같은 인덱스에 존재하는 여러 데이터들 중 원하는 데이터를 찾으려면 해당 인덱스에 연결된 연결 리스트를 조사해야 한다. 하나의 인덱스에 연결된 연결리스트의 크기가 n일 때, 그 중 원하는 데이터를 찾을 때 최악의 경우 n번이나 (즉, 연결 리스트 모두를) 조사해야 한다. 이 경우 시간복잡도는 O(n)이 된다. 원래 해시 테이블은 고유한 키를 통해 원하는 값을 찾는 구조라 O(1)이 되어 더 효율적으로 자료를 찾을 수 있는 구조인데, 이러한 해시 테이블의 장점이 사라지는 단점이 존재한다. 정말 최악의 경우, 모든 데이터가 하나의 버킷에 연결리스트로 모두 연결되어 해시 테이블에 남는 자리가 많아지고, 노드 생성에 따른 메모리 용량도 추가되어 메모리 공간 낭비로 이어질 수도 있다. 

## 개방 주소법 (opening addressing)

해시 충돌이 발생할 때마다 외부 메모리를 추가적으로 사용하는 chaining 방법과 다르게, 개방 주소법은 해시 충돌이 발생할 경우, 삽입하고자 하는 값을 다른 빈 버킷을 찾아 그 버킷에 데이터를 저장하는 방법이다. 한마디로 “여기 화장실 칸에 사람있는데요? 다른 빈 칸 이용하세요” 방법이다. 그래서 개방 주소법은 추가적인 외부 메모리 사용이 필요하지 않다. 

이러한 개방 주소법을 이용하려면 빈 버킷을 찾는 방법이 필요하다. 빈 버킷을 찾는 방법에는 다음이 존재한다.

- Linear probing(선형 탐색) : 현재 버킷으로부터 고정폭 만큼 이동하여 비어있는 버킷을 검색하는 방법이다. 예를 들면 1칸씩 다음 버킷으로 이동하여 빈 버킷이 있는지 검색하는 것이다. 맨 처음 화장실 칸부터 시작해서 차례대로 노크를 해서 빈 칸이 있는지 확인하는 것이다. 만약 값이 존재하는 버킷들이 어떤 특정 영역에 쏠려 있는 경우, 그 만큼 탐색을 해야 하기에 조금 비효율적일 수 있다.
- Quadratic probing(제곱 탐색) : 값이 존재하는 버킷들이 특정 영역에 쏠려있는 경우 검색 효율이 떨어지는 단점을 보완하기 위한 방법. 고정폭만큼 이동하는 것이 아니라 처음엔 한 칸 이동, 그 다음엔 4칸 이동과 같은 방식으로 1→4→9→16→25→… 칸씩 제곱수만큼 이동하여 빈 버킷을 검색하는 방법이다. 중간에 빈 버킷이 있을 수도 있음에도 건너뛰어버리는 단점이 있다.
- Double Hashing Probing (이중 해싱 탐색) : 해시값을 한 번 더 해싱하여 해시 충돌을 피하는 방법. 해시값을 한 번 더 해싱하기 때문에 그만큼 연산량이 늘어나게 된다.

![[4] 참고.](/images/2023-06-01/2023-06-01-data-structure-hash-table-1.png)

[4] 참고.

# 해시 테이블 구현 예제

해시 테이블을 구현해보자. 

다음은 chaining 기법을 이용하고, 해시 함수는 digit folding 방식으로 얻은 값을 다시 division method로 해시값을 구하는 방법을 택한 해시 테이블 구현 코드이다. 연결 리스트를 사용하기에 [연결 리스트 (linked list)](/data%20structure/data-structure-linked-list/) 문서에서 구현한 연결리스트를 import 하여 사용한다.

- 구현 코드
    
    ```python
    from linked_list_kv import Node, LinkedList
    
    class HashTable():
        """
        해시 함수: digit folding과 division method 방식 사용.\n
        해시 충돌 해결법: chaining 방식 사용.
        """
        def __init__(self, size: int) -> None:
            """
            매개변수 설명)\n
            size: 해시테이블 크기. (총 버킷 수)
            """
            self.size = size
            self.buckets: list[LinkedList] = []
            self.load_factor = 0.75  # 부하율
            self.threshold = self.size * self.load_factor
            self.__createHashTable()
    
        def getAllData(self) -> list[tuple]:
            """
            해시 테이블 내 모든 데이터 반환.
            반환 형태: [(key, value), (key, value), ...]
            """
            all_data = []
            for ll in self.buckets:
                all_data.extend(ll.getAllData())
            return all_data
    
        def __createHashTable(self) -> None:
            """
            해시테이블 생성.\n
            각 버킷마다 빈 연결 리스트를 생성한다.
            """
            for i in range(self.size):
                self.buckets.append(LinkedList())
    
        def hashKey(self, key) -> int:
            """
            키의 해싱값 (숫자) 반환. 
            키가 문자열일 경우로 상정하고 실행.
            만약 키가 문자열이 아닌 int, float 같은 경우,
            문자열로 형 변환 시킨다.
            """
            if type(key) != str:
                key = str(key)
    
            unicode_value_total = 0
            for one_char in key:
                unicode_value_total += ord(one_char)
            
            hash_value = unicode_value_total % self.size
            return hash_value
    
        def addData(self, new_data: tuple) -> None:
            """
            해시 테이블에 새 키-값 데이터 삽입.\n
            만약 기존의 키에 연결된 값을 바꾸기 위해 
            기존 키를 입력한 경우, 해당 버킷의 값만 바꾼다.
            new_data: (key, value)
            """
            key, value = new_data
            index = self.hashKey(key)
            target_linked_list = self.buckets[index]
            data_to_node = Node(key, value)
            target_linked_list.addNodeBack(data_to_node)
    
            # 해시 테이블 크기 자동 재조정
            self.autoResize()
    
        def addDataAll(self, new_dataset: list[tuple]) -> None:
            """
            여러 데이터들을 한꺼번에 해시 테이블에 삽입한다.\n
            """
            for kv in new_dataset:
                self.addData(kv)
    
        def removeData(self, target_key) -> None:
            """
            지정된 키와 일치하는 키-값 데이터 삭제.
            """
            index = self.hashKey(target_key)
            target_ll = self.buckets[index]
            target_ll.deleteNodeByKey(target_key)
    
        def findData(self, key) -> any:
            """
            주어진 key에 대응되는 값을 반환.
            """
            index = self.hashKey(key)
            target_linked_list = self.buckets[index]
            return target_linked_list.getValue(key)
        
        def getLength(self) -> int:
            """
            현재 저장된 데이터 수 반환. 
            해시테이블의 전체 크기를 반환하는 것이 아니라, 
            빈 버킷은 제외하고 기존 데이터의 수만 계산하여 반환. 
            """
            all_data = self.getAllData()
            return len(all_data)
        
        def getHTSize(self) -> int:
            """
            현재 해시테이블의 크기 반환.
            """
            self.size = len(self.buckets)
            return self.size
    
        def printCurrentHT(self) -> None:
            """
            현재 해시테이블에 저장된 데이터 구조 출력.
            해시 충돌로 인해 한 버킷에 여러 노드가 연결되어 있는 상태도
            보여줘야 함.
            """
            for i, ll in enumerate(self.buckets):
                print(f"index: {i}, in a bucket: {ll}")
    
        def clear(self) -> None:
            """
            해시 테이블을 모두 비운다.
            즉, 모든 데이터를 지운다.
            """
            for ll in self.buckets:
                ll.clear()
            
        def autoResize(self) -> None:
            """
            해시 테이블의 크기를 재조정함.
            """
            current_data_len = self.getLength()
            if self.threshold > current_data_len:
                # 저장된 데이터의 수가 해시 테이블의 전체 크기 대비
                # 부하율을 넘지 않은 경우, 아무런 작업도 하지 않음.
                return
            
            new_size = 2 * self.getHTSize() + 1
            self.size = new_size
            new_table = [LinkedList() for i in range(new_size)]
            for key, value in self.getAllData():
                new_index = self.hashKey(key)
                target_ll = new_table[new_index]
                target_ll.addNodeBack(Node(key, value))
            self.buckets = new_table
            self.threshold = self.size * self.load_factor
            
        def checkHashCollision(self) -> tuple[bool, int, int]:
            """
            현재 해시 테이블에 해시 충돌이 일어났는지 확인. 
            한 버킷의 연결리스트의 노드가 둘 이상일 경우 해시 충돌이 일어난 것임. 
            현재 해시 테이블에서 한 버킷이 최대로 가지는 노드 수도 같이 반환.\n
            return type)\n
            (bool, col_depth, no_buckets) 
            -> (해시 충돌 여부, 버킷 당 최대 연결리스트 길이, 해시 충돌난 버킷의 수)
            """
            depth_of_buckets = []
            is_collision = False
            for ll in self.buckets:
                ll_length = ll.getLength()
                if ll_length > 1:
                    is_collision = True
                depth_of_buckets.append(ll_length)
            no_buckets = 0
            no_buckets = sum(map(lambda x: 1 if x > 1 else 0, depth_of_buckets))
            return (is_collision, max(depth_of_buckets), no_buckets)
    
    # 출력 테스트 모음
    def print_borderline(func: callable):
        def wrapper(*args, **kwargs):
            print("=============================")
            result = func(*args, **kwargs)
            print("=============================")
            return result
        return wrapper
    
    @print_borderline
    def print_hashtable_test(dataset: list[tuple], size: int):
        ht = HashTable(size)
        ht.addDataAll(dataset)
        ht.printCurrentHT()
        print(ht.checkHashCollision())
        print(ht.getHTSize())
    
    @print_borderline
    def print_hashtable_with_bigger_size_test(
            old_dataset: list[tuple],
            old_size: int,
            new_dataset: list[tuple]
            ):
        ht = HashTable(old_size)
        ht.addDataAll(old_dataset)
        ht.printCurrentHT()
        print(f"현재 해시테이블 크기: {ht.getHTSize()}")
        print(f"현재 저장된 데이터 수: {ht.getLength()}")
        print("===========")
    
        ht.addDataAll(new_dataset)
        ht.printCurrentHT()
        print(f"현재 해시테이블 크기: {ht.getHTSize()}")
        print(f"현재 저장된 데이터 수: {ht.getLength()}")
        
    
    if __name__ == '__main__':
        dataset = [
            ("사과", "apple"),
            ("바나나", "banana"),
            ("딸기", "strawberry"),
            ("오렌지", "orange"),
            ("포도", "grape"),
            ("꽃", "flower"),
            ("나무", "tree"),
            ("바다", "sea"),
            ("별", "star"),
            ("햇님", "sun")
        ]
        # 원하는 테스트 코드의 주석만 해제하여 실행.
        print_hashtable_test(dataset, 10)
        #print_hashtable_test(dataset, 23)
        #print_hashtable_test(dataset, 47)
        #print_hashtable_test(dataset, 64)
        #print_hashtable_test(dataset, 100) # 해시 충돌 없음.
    
        new_dataset = [
            ("코딩", "coding"),
            ("밤바다", "night sea"),
            ("공기", "air"),
            ("공깃밥", "rice"),
            ("디버그", "debug"),
            ("에너지", "energy"),
        ]
        #print_hashtable_with_bigger_size_test(dataset, 2*len(dataset)+1, new_dataset)
    ```
    
    ```python
    import unittest
    from my_hash_table import HashTable
    
    dataset = [
        ("사과", "apple"),
        ("바나나", "banana"),
        ("딸기", "strawberry"),
        ("오렌지", "orange"),
        ("포도", "grape"),
        ("꽃", "flower"),
        ("나무", "tree"),
        ("바다", "sea"),
        ("별", "star"),
        ("햇님", "sun")
    ]
    new_dataset = [
            ("코딩", "coding"),
            ("밤바다", "night sea"),
            ("공기", "air"),
            ("공깃밥", "rice"),
            ("디버그", "debug"),
            ("에너지", "energy"),
    ]
    
    class TestHashTable(unittest.TestCase):
        def setUp(self) -> None:
            self.ht = HashTable(10)
            self.dataset = dataset
            self.desc = self.shortDescription()
    
            if self.desc == "need_dataset":
                self.ht.addDataAll(self.dataset)
        
        def test_empty_data(self):
            """
            빈 해시 테이블 출력 테스트.
            """
            self.assertEqual(self.ht.getAllData(), [])
            self.assertEqual(self.ht.getLength(), 0)
            self.assertEqual(self.ht.getHTSize(), 10)
    
        def test_clear(self):
            """
            need_dataset\n
            해시 테이블 내 모든 데이터를 삭제하는 지 테스트.
            """
            self.ht.clear()
            self.assertEqual(self.ht.getAllData(), [])
            self.assertEqual(self.ht.getLength(), 0)
            self.assertEqual(self.ht.getHTSize(), 21)
    
        def test_add_data(self):
            """
            (key, value) 데이터 삽입 테스트.
            """
            # test1
            self.ht.addData(('우와', 'wow'))
            self.ht.addData(('방', 'room'))
            saved_data = self.ht.getAllData()
            saved_data.sort()
            expected_result = [('우와', 'wow'), ('방', 'room')]
            expected_result.sort()
            self.assertEqual(saved_data, expected_result)
            self.assertEqual(self.ht.getLength(), 2)
    
            # test2
            self.ht.addData(('우와', 'WOW'))
            saved_data = self.ht.getAllData()
            saved_data.sort()
            expected_result = [('우와', 'WOW'), ('방', 'room')]
            expected_result.sort()
            self.assertEqual(saved_data, expected_result)
            self.assertEqual(self.ht.getLength(), 2)
    
        def test_add_data_all(self):
            """
            need_dataset\n
            데이터셋을 잘 삽입하는지 테스트.
            """
            actual_result = self.ht.getAllData()
            actual_result.sort()
            self.dataset.sort()
            self.assertEqual(actual_result, self.dataset)
    
        def test_find_data(self):
            """
            need_dataset\n
            주어진 key에 대응되는 값을 찾는 테스트.
            """
            # test1
            test_key = '꽃'
            result = self.ht.findData(test_key)
            self.assertEqual(result, "flower")
    
            # test2
            test_key = "불"
            result = self.ht.findData(test_key)
            self.assertEqual(result, None)
    
        def test_extend_ht_size(self):
            """
            need_dataset\n
            autoResize() 메서드 테스트. 
            """
            old_hash_collision = self.ht.checkHashCollision()
            old_ht_size = self.ht.getHTSize()
            test_old_data = ("꽃", "flower")
            old_hash_code = self.ht.hashKey(test_old_data[0])
    
            self.ht.addDataAll(new_dataset)
            new_hash_collision = self.ht.checkHashCollision()
            new_ht_size = self.ht.getHTSize()
            new_hash_code = self.ht.hashKey(test_old_data[0])
    
            self.assertEqual(old_hash_collision[0], True)
            self.assertTrue(old_hash_collision[1] > new_hash_collision[1])
            self.assertEqual(new_hash_collision[0], True)
            self.assertNotEqual(old_ht_size, new_ht_size)
            self.assertNotEqual(old_hash_code, new_hash_code)
            self.assertEqual(
                self.ht.findData(test_old_data[0]),
                test_old_data[1]
                )
    
        def test_check_hash_collision(self):
            """
            해시 충돌 여부 확인 테스트.
            """
            # test1
            ht = HashTable(10)
            ht.addDataAll(self.dataset)
            is_col, max_depth, num_buckets = ht.checkHashCollision()
            self.assertEqual(is_col, True)
            self.assertEqual(max_depth, 3)
            self.assertEqual(num_buckets, 2)
    
            # test2
            ht = HashTable(100)
            ht.addDataAll(self.dataset)
            is_col, max_depth, num_buckets = ht.checkHashCollision()
            self.assertEqual(is_col, False)
            self.assertEqual(max_depth, 1)
            self.assertEqual(num_buckets, 0)
    
        def test_remove_data(self):
            """
            need_dataset\n
            해시 테이블 내 특정 데이터를 삭제하는 removeData() 
            메서드 테스트.
            """
            target_data = ("포도", "grape")
            self.ht.removeData(target_data[0])
            all_data = self.ht.getAllData
            self.assertNotIn(all_data, target_data)
    
    if __name__ == '__main__':
        unittest.main()
    ```
    
    ```python
    ........
    ----------------------------------------------------------------------
    Ran 8 tests in 0.002s
    
    OK
    ```
    

---

Reference

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)

[2]

[해시 테이블](https://ko.wikipedia.org/wiki/해시_테이블)

[3]

[[자료구조/알고리즘] - 해시 테이블](https://velog.io/@roro/자료구조알고리즘-해시-테이블)

[4]

[[자료구조] 해시테이블(HashTable)이란?](https://mangkyu.tistory.com/102)

[5]

[[자료구조]해싱, 해시 테이블 그리고 Java HashMap](https://velog.io/@adam2/자료구조해시-테이블)

[6]

[[자료구조] Hash의 개념 및 설명](https://go-coding.tistory.com/30)