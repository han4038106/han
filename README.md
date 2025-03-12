# han
headname
##git꺼리문화


import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

class Ammo {
    String dodic;
    String lot;
    int rounds;

    public Ammo(String dodic, String lot, int rounds) {
        this.dodic = dodic;
        this.lot = lot;
        this.rounds = rounds;
    }
}

public class AmmoDistributionSystem {
    private static final int PALLET_THRESHOLD = 86400; // 1파렛트 기준 발수
    private static final int BOX_CAPACITY = 1680; // 1박스 = 1680발
    private static final int CAN_CAPACITY = 840; // 1캔 = 840발
    private List<Ammo> ammoData = new ArrayList<>();

    public void addAmmo(String dodic, String lot, int rounds) {
        ammoData.add(new Ammo(dodic, lot, rounds));
    }

    public void calculateRequirements() {
        int totalRounds = ammoData.stream().mapToInt(a -> a.rounds).sum();
        List<String> requiredItems = new ArrayList<>();

        System.out.println("\n탄약 불출 상세 내역:");
        for (Ammo ammo : ammoData) {
            int pallets = ammo.rounds / PALLET_THRESHOLD;
            int remainingAfterPallets = ammo.rounds % PALLET_THRESHOLD;
            int remainingAfterBoxes = remainingAfterPallets % BOX_CAPACITY;
            int cans = remainingAfterBoxes / CAN_CAPACITY;
            int looseRounds = remainingAfterBoxes % CAN_CAPACITY;
            
            System.out.println("DODIC: " + ammo.dodic + ", LOT: " + ammo.lot);
            System.out.println(" - 파렛트: " + pallets + "개");
            System.out.println(" - 캔통: " + cans + "개");
            
            if (looseRounds > CAN_CAPACITY) {
                System.out.println(" - 낱발: " + CAN_CAPACITY + "발 + " + (looseRounds - CAN_CAPACITY) + "발");
            } else if (looseRounds > 0) {
                System.out.println(" - 낱발: " + looseRounds + "발");
            }
        }

        // 지게차 및 유도세트 필요 여부
        if (totalRounds >= PALLET_THRESHOLD) {
            requiredItems.add("지게차");
            requiredItems.add("지게차 유도세트");
        }

        // 낱발이 있는 경우 빈캔통 필요
        int uniqueCanNeeded = 0;
        for (Ammo ammo : ammoData) {
            int remaining = ammo.rounds % BOX_CAPACITY;
            if (remaining > CAN_CAPACITY) {
                uniqueCanNeeded++;
            }
        }

        if (uniqueCanNeeded > 0) {
            requiredItems.add("빈캔통 " + uniqueCanNeeded + "개");
        }

        // 결과 출력
        System.out.println("\n필요한 물품:");
        for (String item : requiredItems) {
            System.out.println("- " + item);
        }
    }

    public void run() {
        Scanner scanner = new Scanner(System.in);
        System.out.println("=== 탄약 불출 시스템 ===");
        
        while (true) {
            System.out.print("DODIC 입력: ");
            String dodic = scanner.nextLine();
            System.out.print("LOT 입력: ");
            String lot = scanner.nextLine();
            System.out.print("발수 입력: ");
            int rounds = scanner.nextInt();
            scanner.nextLine(); // 버퍼 정리

            addAmmo(dodic, lot, rounds);

            System.out.print("추가 입력? (y/n): ");
            String more = scanner.nextLine();
            if (!more.equalsIgnoreCase("y")) {
                break;
            }
        }

        calculateRequirements();
        scanner.close();
    }

    public static void main(String[] args) {
        AmmoDistributionSystem system = new AmmoDistributionSystem();
        system.run();
    }
}
