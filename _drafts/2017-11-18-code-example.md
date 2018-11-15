---
title: 내용 테스트 두번째.
---

### 국민에 대하여

모든 국민은 자기의 행위가 아닌 친족의 행위로 인하여 불이익한 처우를 받지 아니한다. 선거와 국민투표의 공정한 관리 및 정당에 관한 사무를 처리하기 위하여 선거관리위원회를 둔다. 정당의 목적이나 활동이 민주적 기본질서에 위배될 때에는 정부는 헌법재판소에 그 해산을 제소할 수 있고, 정당은 헌법재판소의 심판에 의하여 해산된다.

대통령은 필요하다고 인정할 때에는 외교·국방·통일 기타 국가안위에 관한 중요정책을 국민투표에 붙일 수 있다. 감사원은 세입·세출의 결산을 매년 검사하여 대통령과 차년도국회에 그 결과를 보고하여야 한다. 공공필요에 의한 재산권의 수용·사용 또는 제한 및 그에 대한 보상은 법률로써 하되, 정당한 보상을 지급하여야 한다.

---

```java
package Calculator.context;

public final class StringArrayContext extends Context<String[]> {
    // commemnt
    protected StringArrayContext(String[] data) {
        super(data);
    }

    public StringArrayContext trim() {
        String[] ret = new String[data.length]; // 3이 되어야 한다.

        for (int i = 0; i < data.length; i++) {
            ret[i] = data[i].trim();
        }

        data = ret;

        return this;
    }

    public IntegerArrayContext toInts() {
        int[] ret = new int[data.length];

        for (int i = 0; i < data.length; i++) {
            ret[i] = Integer.parseInt(data[i]);
        }

        return Context.of(ret);
    }
}
```

### 국민에 대하여

모든 국민은 자기의 행위가 아닌 친족의 행위로 인하여 불이익한 처우를 받지 아니한다. 선거와 국민투표의 공정한 관리 및 정당에 관한 사무를 처리하기 위하여 선거관리위원회를 둔다. 정당의 목적이나 활동이 민주적 기본질서에 위배될 때에는 정부는 헌법재판소에 그 해산을 제소할 수 있고, 정당은 헌법재판소의 심판에 의하여 해산된다.

```javascript
import { Chatting } from "components/Chatting";
import { Calendar } from "components/Calendar";
import { TextEditor } from "components/TextEditor";
import { GridLayoutItemType } from "interface/GridLayout";

class ComponentResolver {
  map = new Map();

  constructor() {
    this.map.set(GridLayoutItemType.CHATTING, Chatting);
    this.map.set(GridLayoutItemType.CALENDAR, Calendar);
    this.map.set(GridLayoutItemType.TEXT_EDITOR, TextEditor);
  }

  getComponent(componentType: string) {
    return this.map.get(componentType);
  }
}

const resolver = new ComponentResolver();

export const getComponent = (componentType: string) => {
  return resolver.getComponent(componentType);
};
```
