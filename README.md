# 11328230
import random

class Player:
    def __init__(self, name):
        self.name = name
        self.position = 0
        self.coins = 0
        self.stars = 0
        self.partner = None
        self.items = []

    def move(self, board_size):
        roll = random.randint(1, 6)
        self.position = (self.position + roll) % board_size
        print(f"{self.name} 掷骰子得 {roll}，移動到位置 {self.position}")
        return roll

    def earn_coins(self, amount):
        if self.partner == "雙倍金幣":
            amount *= 2
        self.coins += amount
        print(f"{self.name} 獲得了 {amount} 金幣，現在共有 {self.coins} 金幣")

    def earn_star(self):
        self.stars += 1
        print(f"{self.name} 獲得了一顆星星，現在共有 {self.stars} 顆星星")

    def buy_item(self, item, cost):
        if self.coins >= cost:
            self.coins -= cost
            self.items.append(item)
            print(f"{self.name} 購買了 {item}，剩餘 {self.coins} 金幣")
        else:
            print(f"{self.name} 金幣不足，無法購買 {item}")

    def steal_coins(self, other_player):
        stolen_amount = min(20, other_player.coins)
        self.coins += stolen_amount
        other_player.coins -= stolen_amount
        print(f"{self.name} 搶奪了 {stolen_amount} 金幣來自 {other_player.name}！")

def create_board():
    return ["普通格子"] * 15 + ["商店", "搶奪格子", "星星格子", "夥伴格子"]

def play_game():
    players = [Player("玩家1"), Player("玩家2"), Player("玩家3"), Player("玩家4")]
    board = create_board()
    items_for_sale = {"金幣加倍卡": 50, "星星加倍卡": 100}
    partners = ["雙倍金幣", "雙倍星星"]
    rounds = 10

    print("歡迎來到大富翁遊戲！")

    for round_num in range(1, rounds + 1):
        print(f"\n==== 第 {round_num} 回合 ====")
        
        for player in players:
            roll = player.move(len(board))
            current_tile = board[player.position]

            if current_tile == "普通格子":
                player.earn_coins(roll * 10)
            elif current_tile == "商店":
                print(f"{player.name} 來到了商店，可以購買道具：")
                for item, cost in items_for_sale.items():
                    print(f"{item} - {cost} 金幣")
                choice = input(f"{player.name} 是否要購買道具？(輸入道具名稱或 n 跳過): ").strip()
                if choice in items_for_sale:
                    player.buy_item(choice, items_for_sale[choice])
            elif current_tile == "搶奪格子":
                target = random.choice([p for p in players if p != player])
                player.steal_coins(target)
            elif current_tile == "星星格子":
                player.earn_star()
            elif current_tile == "夥伴格子":
                partner = random.choice(partners)
                player.partner = partner
                print(f"{player.name} 獲得了夥伴 {partner}！")

        # 星星兌換機制
        for player in players:
            while player.coins >= 100:
                player.coins -= 100
                player.earn_star()

    # 遊戲結束統計
    print("\n==== 遊戲結束 ====")
    for player in players:
        print(f"{player.name} 擁有 {player.stars} 顆星星，{player.coins} 金幣")
    
    # 判斷勝利者
    players.sort(key=lambda p: (p.stars, p.coins), reverse=True)
    winner = players[0]
    print(f"恭喜 {winner.name} 獲勝！")

if __name__ == "__main__":
    play_game()
