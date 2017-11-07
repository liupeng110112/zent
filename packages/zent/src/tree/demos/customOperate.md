---
order: 5
zh-CN:
	title: 自定义操作
	index: 首页
	tree: 树
en-US:
	title: Custom Operate
	index: Index
	tree: Tree
---

```jsx
import { Tree, Icon, Radio } from 'zent';

const RadioGroup = Radio.Group;
const originData = [{
	id: 1,
	title: '杭州有赞科技有限公司',
}, {
	id: 2,
	title: '技术',
	parentId: 1
}, {
	id: 3,
	title: '后端',
	parentId: 2
}, {
	id: 4,
	title: '运维',
	parentId: 2
}, {
	id: 5,
	title: '前端',
	parentId: 2
}, {
	id: 6,
	title: '产品',
	parentId: 1
}];

const deepClone = (node, parentId = 0, nodeArray = []) => {
	const copyNode = {
		id: String(Math.random()).replace('0.', ''),
		parentId,
		title: node.title
	};
	nodeArray.push(copyNode);
	
	for (let i = 0, l = node.children && node.children.length || 0; i < l; i++) {
		deepClone(node.children[i], copyNode.id, nodeArray);
	}
	return nodeArray;
}

class TreeExample extends React.Component {
	state = {
		treeData: originData,
		copyType: 'shallow' 
	}

	onDelete = (data) => {
		this.setState({
			treeData: this.state.treeData.filter(item => item.id !== data.id)
		});
	};

	onClone = (data) => {
		const { copyType } = this.state;
	
		if (copyType === 'shallow') {
			const node = Object.assign({}, data, { id: Date.now() });
			this.setState({
				treeData: [...this.state.treeData, node]
			});
		} else if (copyType === 'deep') {
			const nodeArray = deepClone(data, data.parentId);
			this.setState({
				treeData: [...this.state.treeData, ...nodeArray]
			});
		}
	};

	onCopyTypeChange = (e) => this.setState({ copyType: e.target.value });

	render() {
		const { copyType, treeData } = this.state;
		const operations = [{
			name: 'Delete',
			icon: <Icon type="close" />,
			action: this.onDelete
		}, {
			name: 'Clone',
			icon: <Icon type="plus" />,
			action: this.onClone
		}];
	
		return (
			<div>
				<RadioGroup onChange={this.onCopyTypeChange} value={copyType}>
					<Radio value="shallow">浅拷贝</Radio>
					<Radio value="deep">深拷贝</Radio>
				</RadioGroup>
				<hr/>
				<Tree data={treeData} dataType="plain" operations={operations} />
			</div>
		);
	}
}

ReactDOM.render(
	<TreeExample />
	, mountNode
)
```